web3-plugin-polkadot
===========
This is a web3.js plugin for communicating with Polkadot, Kusama, and Substrate nodes.

Project name: **web3-plugin-polkadot**

Project creation date: **29 Nov 2023**

## Introduction
As web3.js v4.x supports plugins, this library utilizes that to build a plugin for Polkadot RPC providers. This plugin allows users to execute calls like `web3.polka.substrate.namespace.RPC` and similar commands for Polkadot RPCs. It will later offer additional functionalities related to accounts & contracts, among others.

## Why to build
There are 3 key benefits of this approach:

### Development experience and learning curve
Developers who already use web3.js would quickly start working with Polkadot and Substrate with fewer things to read and learn. All they need to do is provide the web3.js library with a link to a Polkadot, Kusama, or a Substrate provider, register the new plugin, and then start working just like they used to. There will be slight differences that they'll have to adjust for. And a good progressive effort would be spent to reduce the differences to the minimum.

### Code portability
Code that already exists for Ethereum DApps, that uses web3.js, could be relatively-easily adapted to work with Polkadot and Substrate. Users would only have to make a few changes to their current code. For example, the code used to catch exceptions that indicate an unresponsive provider won't need to be changed. This also means developers may use the same code base for both Ethereum-based networks and Substrate, with just a bit of custom handling.

### Diversity
It is good to provide the developers with an additional alternative to polkadot{.js}. So, developers can pick the library that they feel more comfortable working with.

## Methodology

This plugin uses web3.js processing and logic; However, the types from `@polkadot/types` in addition to types generated by `@polkadot/typegen` are utilized. Using those types keeps things smooth when communicating with calling the RPC methods. In addition to keeping the available documentation for those types mostly useful, instead of inventing new types. 
On the other hand, having the execution inside web3.js allows for having the benefits listed above in the [Why to build](#why-to-build) section.  

## TypeScript IntelliSense

This package utilizes IntelliSense, in order to provide the developers with the best development experience possible. So, they can use code auto-complete and hover over a method to see the method parameters and return type. And they can get a compiler error when messing up with a method name or its parameters, or when wrongly assigning its return type. 

Actually, the RPCs are accessible with the convention: `web3.polka.<network>.<namespace>.<method>`. Where the out-of-the-box supported network nodes are: `polkadot`, `kusama` and `substrate`. And, the user can add for any custom RPC and still have the IntelliSense according to the provided custom types. 

Let us take a look at how IntelliSense is provided using this plugin.

First of all, the plugin provides a list of supported networks out-of-the-box. And here are those 3:

![polka.network](https://github.com/conx3/hackathon-2023-winter/blob/web3-plugin-polkadot/projects/32-web3-plugin-polkadot/docs/images/IntelliSense-web.polka.png)
The above list can be expanded by the developer, as you can see in the next section.

Facilitating easy access to Rpc methods, here is how the namespaces that categorize the RPC methods are listed inside the `polkadot` network namespace:

![polkadot rpcs organized in namespaces](https://github.com/conx3/hackathon-2023-winter/blob/web3-plugin-polkadot/projects/32-web3-plugin-polkadot/docs/images/IntelliSense-polkadot-dot-with-filter.png)


And, here are the RPC methods listed inside `substrate` network namespace:

![substrate rpcs organized in namespaces](https://github.com/conx3/hackathon-2023-winter/blob/web3-plugin-polkadot/projects/32-web3-plugin-polkadot/docs/images/IntelliSense-substrate-dot-with-filter.png)

Note that the `substrate` methods above are not the same as for `polkadot`.

As seen above, some RPC methods are not supported on all networks. Thus, the plugin takes this into consideration and does not show them to the developer to avoid confusion, especially for those new in this space.

And here is how the RPC methods, inside a specific namespace, would be listed:

![rpc methods](https://github.com/conx3/hackathon-2023-winter/blob/web3-plugin-polkadot/projects/32-web3-plugin-polkadot/docs/images/IntelliSense-web3.polka.polkadot.chain-dot.png)

Picking or hovering over a specific method like `getBlock` would show its parameters and return type:

![method parameters and return type](https://github.com/conx3/hackathon-2023-winter/blob/web3-plugin-polkadot/projects/32-web3-plugin-polkadot/docs/images/IntelliSense-getblock.png)

Hint: The types are provided by utilizing the types generated by `typegen` after filtering them according to the lists saved after calling the `rpc_methods` endpoint on the desired network nodes.

### Supported Custom Rpc Methods

IntelliSense also works well with custom Rpc methods.
So, if the developer registers for a specific network and names it, for example, `MyCustomNetwork`, this is how the list of networks would appear:

![polka.network](https://github.com/conx3/hackathon-2023-winter/blob/web3-plugin-polkadot/projects/32-web3-plugin-polkadot/docs/images/intellisense-mycustomnetwork.png)

Communicating with custom nodes or custom networks is very handy in the Polkadot ecosystem as, for example, Substrate is built to support easy configuration and customization in addition to smooth upgrades.

Here is a simple example of if a custom node only exposes Rpc methods of `chain` and `system`:

![polka.network](https://github.com/conx3/hackathon-2023-winter/blob/web3-plugin-polkadot/projects/32-web3-plugin-polkadot/docs/images/intellisense-mycustomnetwork-chain.png)

## Using the plugin

To check things quickly, clone this repo, go to the `test` folder, update or keep the code, and then run `yarn && yarn test` in the terminal.

Or import and use the plugin package inside your TypeScript project following these steps:

1. In your TypeScript project run:
`yarn add web3 @conx3/web3-plugin-polkadot`

1. After that, use something like the following code to call the RPC methods:

```typescript
import { Web3 } from 'web3';
import { PolkaPlugin } from '@conx3/web3-plugin-polkadot';

// the following shows how to use the plugin
// you can call any rpc method using the convention: web3.polka.<network>.<namespace>.<method>
async function main() {
  const web3 = new Web3('wss://rpc.polkadot.io');
  const polkadotPlugin = new PolkaPlugin();
  web3.registerPlugin(polkadotPlugin);
  // to get the last block from Polkadot network
  const polkadotBlockData = await web3.polka.polkadot.chain.getBlock();

  // call web3 methods as usual. Like updating the provider to Kusama network:
  web3.provider = 'wss://kusama-rpc.polkadot.io';
  // to get the last block from Kusama network
  const kusamaBlockData = await web3.polka.kusama.chain.getBlock();

  // Updating the provider to a local substrate node for example:
  web3.provider = 'ws://127.0.0.1:9944/';
  // to get the last block from a Substrate network
  const substrateBlockData = await web3.polka.substrate.chain.getBlock();

  console.log('polkadot block header stateRoot:', polkadotBlockData.block.header.stateRoot);
  console.log('kusama block header stateRoot:', kusamaBlockData.block.header.stateRoot);
  console.log('substrate block header stateRoot:', substrateBlockData.block.header.stateRoot);

  // stateRoot is something like: 0xa18402bc3a2249d6af8e2ad6241e5b1b60360abd1b4e2c7c733c8c980331d278

  // if you want to log the full response
  console.log(JSON.stringify(substrateBlockData, null, 2));
  // {
  //   "jsonrpc": "2.0",
  //   "result": {
  //     "block": {
  //       "header": {
  //         "parentHash": "0x205d46cdcd9db4f795067718ef73292ab065aa08cec1ad6788b2c24028b160ea",
  //         "number": "0x6cc7",
  //         "stateRoot": "0xa18402bc3a2249d6af8e2ad6241e5b1b60360abd1b4e2c7c733c8c980331d278",
  //         "extrinsicsRoot": "0x345fc26b56a2a682a52ab5860b18df0a1698b0a6ac0cadd9bcba713d1a6f54d0",
  //         "digest": {
  //           "logs": [
  //             "0x0661757261203b5ee81000000000",
  //             "0x05617572610101187f7e10b05cd255b4ab0d3894b2c3c15bc4294a4124a7188981e3833af3440ae4322bec54ff65cb561e9fdfb4d02a5496fc64ea5991fcd4d42b43c48cd2588d"
  //           ]
  //         }
  //       },
  //       "extrinsics": [
  //         "0x280401000bd08620468c01"
  //       ]
  //     },
  //     "justifications": null
  //   },
  //   "id": 43
  // }
}
main();
```

## Running the tests

The tests will communicate using a websocket with a local substrate node running at port `9944`. They will also connect to the remote Polkadot and Kusama nodes at `wss://rpc.polkadot.io` and `wss://kusama-rpc.polkadot.io`. So, to have the tests pass, make sure to satisfy the requirements or update the URLs inside the test files located at the `/tests/` folder.

After cloning the repository locally and installing the dependencies, you can run the tests with:
  `yarn test` or `npm test`

And you should see something like:

```js
 PASS  test/polka-plugin.test.ts
  PolkaPlugin Tests
    ✓ should be able to register the plugin (26 ms)
    PolkaPlugin Provider exceptions
      ✓ should throw `InvalidResponseError` when the method is not supported by the provider (simulated) (17 ms)
      ✓ should throw `InvalidResponseError` when the method is not supported by the provider (calling a polkadot method at a substrate node) (1 ms)

 PASS  test/polka-plugin-custom.test.ts
  PolkaPlugin Custom Provider Tests
    ✓ should be able to register custom rpc methods at a custom namespace inside the plugin (37 ms)

 PASS  test/web3.polka.substrate.test.ts
  test some RPC methods at web3.polka.substrate
    ✓ should call chain.getBlock method (8 ms)
    ✓ should chain.getBlock() equals chain.getBlock(chain.getBlockHash(latestBlocHash)) (3 ms)
    ✓ should get the rpc methods
    ✓ should not have the rpc methods that is not available at substrate (for example beefy.[method])

 PASS  test/web3.polka.kusama.test.ts (16.454 s)
  test some RPC methods at web3.polka.kusama
    ✓ should call chain.getBlock method (309 ms)
    ✓ should chain.getBlock() equals chain.getBlock(chain.getBlockHash(latestBlocHash)) (380 ms)
    ✓ should get the rpc methods (376 ms)

 PASS  test/web3.polka.polkadot.test.ts (19.862 s)
  test some RPC methods at web3.polka.polkadot
    ✓ should call chain.getBlock method (430 ms)
    ✓ should chain.getBlock() equals chain.getBlock(chain.getBlockHash(latestBlocHash)) (441 ms)
    ✓ should get the rpc methods (382 ms)
```

Contributing
------------

Pull requests are welcome. For major changes, please open an issue first to discuss what you would like to add or change.

Please make sure to update tests as appropriate when contributing.

Hints:
- Types were generated by following the steps at:
  [https://polkadot.js.org/docs/api/examples/promise/typegen/](https://polkadot.js.org/docs/api/examples/promise/typegen/)
- A local Substrate node can be run following the steps at: 
  [https://docs.substrate.io/quick-start/start-a-node/](https://docs.substrate.io/quick-start/start-a-node/)

License
-------

[MIT](https://choosealicense.com/licenses/mit/)
