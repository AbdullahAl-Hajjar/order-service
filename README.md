# Order Service

Part of the Algonquin Pet Store (CST8915). The order-service is a Node.js/Express API that takes orders from the store-front and publishes them to RabbitMQ (`order_queue`, durable, persistent messages with publisher confirms). It only reports `Order received` after RabbitMQ confirms the message.

## Configuration (12-Factor: Config, Backing services)

Everything that changes between environments comes from environment variables. `dotenv` loads a local `.env` file when one exists, and real environment variables always win.

| Variable | Purpose | Default |
| --- | --- | --- |
| `RABBITMQ_CONNECTION_STRING` | AMQP URL of the RabbitMQ broker, e.g. `amqp://orderapp:<password>@<host>:5672/` | `amqp://localhost` |
| `PORT` | Port the API listens on | `3000` |

Copy `.env.example` to `.env` for local runs. `.env` is ignored by Git, so credentials never end up in the repo.

## Run locally

```bash
npm ci
node index.js
```

Then send a test order (or use `test-order-service.http` with the VS Code REST Client):

```bash
curl -i -X POST http://localhost:3000/orders \
  -H 'Content-Type: application/json' \
  -d '{"product":{"id":1,"name":"Dog Food","price":19.99},"quantity":2,"totalPrice":39.98}'
```

The broker-failure tests in `test/` run without a live RabbitMQ server: `node --test`.

## Azure App Service (Lab 3)

Deployed to the Linux Web App `order-service-app-aa` (Node 24 LTS). App Service starts it with `node index.js` (the `main` entry) and passes its own `PORT`, so the only app setting needed is `RABBITMQ_CONNECTION_STRING`, which points at the RabbitMQ VM.
