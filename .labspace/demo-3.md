## Demo #3 - Test

We just ran the integration tests with Docker Compose in order to test our application with its dependencies.

Another complementary option is also to programmantically write these integration tests as unit tests. Very convenient for your Developers.

Here enters Test Containers:

![](.labspace/images/tcc.png)

### Open the unit tests

Open the file :fileLink[test/integration/productCreation.integration.spec.js]{path="catalog-service-node/test/integration/productCreation.integration.spec.js" line=18}.

We can see that dependencies can be programmatically started as containers:
- `createAndBootstrapPostgresContainer`
- `createAndBootstrapKafkaContainer`
- `createAndBootstrapLocalstackContainer`

### Update the unit tests

Add a new line :fileLink[at line 91]{path="catalog-service-node/test/integration/productCreation.integration.spec.js" line=91}, and add the following to the integration test to verify the field is included in the message when a new product is created:

```js
upc: createdProduct.upc,
```

Save the file.

```diff no-copy-button
await kafkaConsumer.waitForMessage({
     id: createdProduct.id,
     action: "product_created",
+    upc: createdProduct.upc,
   });
```

Run the specific test updated, and make sure it's still running successfully:

```none no-copy-button
 PASS  test/integration/productCreation.integration.spec.js (17.294 s)
  Product creation
    ✓ should publish a Kafka message when creating a product (218 ms)
    ○ skipped should publish and return a product when creating a product
    ○ skipped should upload a file correctly
    ○ skipped doesn't allow duplicate UPCs

Test Suites: 1 passed, 1 total
Tests:       3 skipped, 1 passed, 4 total
Snapshots:   0 total
Time:        17.333 s, estimated 19 s
```

### Use Test Containers in CI

In your CI pipelines you can directly run these unit tests within your CI job runners or you can use the managed Test Containers Cloud (TCC) feature.

Open the file :fileLink[.github/workflows/pipeline-docker-cloud.yaml]{path="catalog-service-node/.github/workflows/pipeline-docker-cloud.yaml" line=88} to see how to integrate this TCC feature in your own CI pipelines:

```yaml no-copy-button
      - name: Setup Testcontainers Cloud Client
        uses: atomicjar/testcontainers-cloud-setup-action@v1
        with:
          token: ${{ secrets.TC_CLOUD_TOKEN }}
```
