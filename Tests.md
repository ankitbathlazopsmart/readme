# Unit and Integration tests

> Provides information for writing unit and integration tests for zode apps.

## Integration tests
> Integration testing is a type of testing meant to check the combinations of different units, their interactions, the way subsystems unite into one common system,   and code compliance with the requirements.

####  1. Here is an example for integration testing for a simple zode app. ( Without mocking )

##### App.ts -
``` typescript
import Zode from '@zode/zode';

const app = new Zode();

app.get('/hello-world', async () => 'Hello World!');

export default app.start();

```
>App.ts consists a simple zode app which contains a get call which when hits returns 'Hello World!'.

##### App.test.ts -

```typescript
import { IntegrationServer} from '@zode/zode';

import app from './App.ts';

const describeDescription = 'Hello-World-App';

describe(describeDescription,() => {
  
  let server: IntegrationServer
  
  beforeAll(async () => {
    server = new IntegrationServer(await app)
  })
  
    afterAll(() => server.teardown())
    
    describe('/hello-world', () => {
    test('should return response when invoked', async () => {
      
      const response = await IntegrationServer.withRequiredHeaders(
        server.zodeServer.get('/hello-world')
      )

      expect(response.body).toEqual({ data: 'Hello World!' })
    })
  })

})
```
>App.test.ts consists the integration test for App.ts.

##### IntegrationServer - 

>The goal of the integration server is be able to provide a clean way to spin up your zode application and run real requests against it.


#### 2. Here is an Example for integration test for a Simple Zode App . ( With mocking )

##### App.ts

```typescript

import Zode, { Context } from "@zode/zode";

const app = new Zode();

app.get("/test", async (ctx: Context) => {

    const res = ctx.httpClient.get(ctx, ctx.logger, "/testURL");
    
    return res
});
```
>App.ts consists a simple zode app which contains a get call which when hits makes a http get requests  which takes context , logger and url as parameter and return the response.

Now we will mock the httpClient request  in the integration test for App.ts.

##### App.test.ts

```typescript
import Zode, {
  HTTPMethod,
  MockedIntegrationServer,
  Context
} from ' @zode/zode'

const describeDescription = "MockIntegrationServer";

describe( describeDescription , () => {
  const server = new MockedIntegrationServer({ APP_NAME: 'anything' })

  server.routes.get('/test', async (ctx: Context) =>
    ctx.httpClient.get(ctx, ctx.logger, '/testURL')
  )

  server.registerHttpClientMock(HTTPMethod.GET, /\/testURL.*/).reply(200, 'hai')

  beforeAll(server.start)
  afterAll(server.teardown)

  test('should respond with 200', async () => {
    const result = await MockedIntegrationServer.withRequiredHeaders(
      server.get('/test')
    )

    expect(result.status).toEqual(200)
    expect(server.logger.log).toHaveBeenCalled()
    expect(server.mostRecentHttpClientCall(HTTPMethod.GET)).toEqual(
      expect.objectContaining({ url: '/testURL' })
    )
  })
})


```
##### MockedIntegrationServer

> the goal of the MockIntegrationServer is to allow us to mock the routes ,HTTPClient request and other features. We can test with the custom Configuration by       passing configuration as a parameter in MockedIntegrationServer.
