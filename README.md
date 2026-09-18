# Online Boutique — Bunnyshell demo

A cloud-native e-commerce demo app, wired up to run on
[Bunnyshell](https://www.bunnyshell.com) as an ephemeral environment.

Adapted from [GoogleCloudPlatform/microservices-demo](https://github.com/GoogleCloudPlatform/microservices-demo)
(Apache 2.0) — see [NOTICE](NOTICE).

## What this is

11 components: a storefront plus the microservices behind it.

| Component | Language | Built from | Purpose |
|---|---|---|---|
| `frontend` | Go | **this repo** | The storefront web UI |
| `productcatalogservice` | Go | upstream image | Product list + search |
| `currencyservice` | Node.js | upstream image | Currency conversion |
| `cartservice` | C# | upstream image | Shopping cart, backed by Redis |
| `recommendationservice` | Python | upstream image | "You may also like" |
| `shippingservice` | Go | upstream image | Shipping quotes |
| `paymentservice` | Node.js | upstream image | Charges the (fake) card |
| `emailservice` | Python | upstream image | Order confirmation |
| `checkoutservice` | Go | upstream image | Orchestrates the order |
| `adservice` | Java | upstream image | Contextual ads |
| `redis-cart` | — | `redis:alpine` | Cart storage |

**Only `frontend` is built from source.** Everything else pulls a pinned
upstream image (`v0.10.7`), so a full deploy is fast and a code change
rebuilds exactly one component.

## Deploy it

```bash
export BUNNYSHELL_TOKEN=<your token>

bns environments create \
  --from-path bunnyshell.yaml \
  --name online-boutique \
  --project <PROJECT_ID> \
  --k8s <CLUSTER_ID>

bns environments deploy --id <ENV_ID> --wait
```

The storefront is published at `boutique-<env-domain>`.

## Making a visible change

Everything on screen is a Go template under `src/frontend/templates/`
and CSS under `src/frontend/static/styles/`. For example, the homepage
headline lives in `src/frontend/templates/home.html`.

Edit, open a pull request, and Bunnyshell builds an ephemeral environment
for that branch with only `frontend` rebuilt.

## Local development

```bash
cd src/frontend
go run .
```

The frontend needs the backing services to be reachable; the simplest way
is `bns port-forward` against a running environment, or point the
`*_SERVICE_ADDR` variables at an existing deployment.
