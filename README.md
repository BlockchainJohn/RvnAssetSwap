# RvnAssetSwap
Swap Assets using the Ravencoin Core

To swap assets using code on Ravencoin, you can use the createrawtransaction, fundrawtransaction, and signrawtransaction RPC commands from the Ravencoin daemon's RPC interface.

Here is an example of how you can use these commands in JavaScript to swap one asset for another:


const rp = require('request-promise');

const createTransaction = async (asset1, qty1, asset2, qty2) => {
  // Create an empty transaction
  const createTxOptions = {
    method: 'POST',
    uri: 'http://localhost:8766/',
    body: {
      jsonrpc: '1.0',
      id: 'curltest',
      method: 'createrawtransaction',
      params: [[], {}}
    },
    json: true
  };

  const createTxResponse = await rp(createTxOptions);
  const txHex = createTxResponse.result;

  // Add inputs to the transaction
  const fundTxOptions = {
    method: 'POST',
    uri: 'http://localhost:8766/',
    body: {
      jsonrpc: '1.0',
      id: 'curltest',
      method: 'fundrawtransaction',
      params: [txHex, {'asset1': qty1, 'asset2': qty2}]
    },
    json: true
  };

  const fundTxResponse = await rp(fundTxOptions);
  const fundedTxHex = fundTxResponse.result.hex;

  // Sign the transaction
  const signTxOptions = {
    method: 'POST',
    uri: 'http://localhost:8766/',
    body: {
      jsonrpc: '1.0',
      id: 'curltest',
      method: 'signrawtransaction',
      params: [fundedTxHex]
    },
    json: true
  };

  const signTxResponse = await rp(signTxOptions);
  const signedTxHex = signTxResponse.result.hex;

  return signedTxHex;
};

(async () => {
  const asset1 = '<asset1-name>:<issuer-name>';
  const qty1 = <quantity-of-asset1>;
  const asset2 = '<asset2-name>:<issuer-name>';
  const qty2 = <quantity-of-asset2>;

  const signedTxHex = await createTransaction(asset1, qty1, asset2, qty2);
  console.log(signedTxHex);

  // Send the signed transaction to the network
  const sendTxOptions = {
    method: 'POST',
    uri: 'http://localhost:8766/',
    body: {
      jsonrpc: '1.0',
      id: 'curltest',
      method: 'sendrawtransaction',
      params: [signedTxHex]
    },
    json: true
  };

  const sendTxResponse = await rp(sendTxOptions);
  console.log(sendTxResponse);
})();
