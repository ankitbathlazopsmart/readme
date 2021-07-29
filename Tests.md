# Unit and Integration tests

> Provides information for writing unit and integration tests for zode apps.

## Integration tests
> Integration testing is a type of testing meant to check the combinations of different units, their interactions, the way subsystems unite into one common system,   and code compliance with the requirements.

#### Here is an example for integration testing for a simple zode app.

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




