## Demo #2 - Develop

## Run the apps and their dependencies

![](.labspace/images/local-stack.png)

Start the stack by running all of the services needed for the application.

```bash terminal-id=main
docker compose up
```

The entire stack started and we don't need any cloud environments. No external data services. Everything is local and starts within seconds. It's all defined by this :fileLink[Compose file]{path="compose.yaml" line=1}, which is in my code repo and shared with the entire team.

Go to the **Demo Client** in the browser: :tabLink[http://localhost:5173]{href="http://localhost:5173" title="Demo Client"}.

Start the **Backend** app:

```bash terminal-id=npm
npm run dev
```

Go to the **Backend** in the browser: :tabLink[http://localhost:3000]{href="http://localhost:3000" title="Backend"}.

Refresh the **Demo Client** in the browser: :tabLink[http://localhost:5173]{href="http://localhost:5173" title="Demo Client"}.

Click the "Create product" button. 

This exercises the catalog service API to create a new product.


Go to the [**Kafbat UI**](http://localhost:8080), and check the [topic in Kafka](http://localhost:8080/ui/clusters/local/all-topics/products/messages?limit=100&mode=LATEST) where the `upc` field is missing.

Let’s at least make sure the database is set up correctly. Open the [**pgAdmin**](http://localhost:5050) tool.

## Fix the issue

An issue was found that we need to fix: [Add UPC code to product_created event](https://github.com/dockersamples/catalog-service-node/issues/19). 

Challenge accepted!

![](.labspace/images/upc-issue.png)

In the :fileLink[src/services/ProductService.js]{path="catalog-service-node/src/services/ProductService.js" line=53} file, update the `publishEvent` call at line 53 to publish the `upc` field:

```js
upc: createdProduct.upc,
```

Save the file.

```diff no-copy-button
  publishEvent("products", {
    action: "product_created",
    id: newProductId,
    name: product.name,
    price: product.price,
    description: product.description,
+   upc: product.upc,
  });
```

When saving the file, you should see the console output indicate the server is restarting to use the latest code.

Validate the fix:
- Refresh the **Demo Client** in the browser: :tabLink[http://localhost:5173]{href="http://localhost:5173" title="Demo Client"}.
- Create a new product in the client
- Confirm that the `upc` field now appears in the published Kafka message in the [**Kafbat UI**](http://localhost:8080).

Congrats!