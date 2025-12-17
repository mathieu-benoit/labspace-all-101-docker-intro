## Demo #3

### Update the test

Open the file :fileLink[test/integration/productCreation.integration.spec.js]{path="test/integration/productCreation.integration.spec.js" line=18}.

Add a new line :fileLink[at line 91]{path="test/integration/productCreation.integration.spec.js" line=91}, and add the following to the integration test to verify the field is included in the message when a new product is created:

```js
upc: createdProduct.upc,
```

Save the file.

```diff
await kafkaConsumer.waitForMessage({
     id: createdProduct.id,
     action: "product_created",
+    upc: createdProduct.upc,
   });
```

### Using TCC in CI

FIXME - GHA worflow not copied
