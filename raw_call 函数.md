# raw_call 函数

raw_call是一个低级函数。它用于调用和发送以太币到其他函数。
对于此示例，我们将在下面编写一个调用此合约的合约。
## TestRawCall.vy

```
# @version ^0.3.7


sender: public(address)


@external
def testAdd(x: uint256, y: uint256) -> uint256:
    self.sender = msg.sender
    return x + y


@external
def testSub(x: uint256, y: uint256) -> uint256:
    self.sender = msg.sender
    return x - y
```

## RawCall.vy

```
# @version ^0.3.7


@external
def testRawCall(to: address, x: uint256, y: uint256):
    res: Bytes[32] = raw_call(
        to, # address to call
        # data to send in Bytes
        concat(
            method_id("testAdd(uint256,uint256)"), # function to call
            convert(x, bytes32), # first input
            convert(y, bytes32), # secode input
        ),
        max_outsize=32, # max size of ouput, default 0
        gas=100000, # optional, amount of gas to send, default forwards all gas
        value=0, # optional, amount of ETH to send, default 0
    )

    z: uint256 = convert(res, uint256)


@external
@payable
def sendEth(to: address):
    # Example to send ETH
    # data = b"", empty data
    # value = msg.value, all of ETH that was sent to this function
    raw_call(to, b"", value=msg.value)


diff: public(uint256)


@external
def testRawCallSub(to: address, x: uint256, y: uint256):
    res: Bytes[32] = raw_call(
        to,
        concat(
            method_id("testSub(uint256,uint256)"),
            convert(x, bytes32),
            convert(y, bytes32)
        ),
        max_outsize=32,
    )

    self.diff = convert(res, uint256)
```
