They are used to trigger smart contracts from a decentralized context based on off-chain conditions.

Read the doc 'Using chainlink keepers'
you contract needs to import and implement this interface:
import "@chainlink/contracts/src/v0.8/automation/interfaces/KeeperCompatibleInterface.sol";
from @chainlink/contracts.

Then implements two functions:
- checkUpkeep that returns a boolean that represents if the upkeep needs to be performed or not. (this function is executed by chainlink nodes off-chain)
- performUpkeep that executes only when the upkeep condition is valid.

```solidity
    function checkUpkeep(
        bytes memory /*checkData*/
    ) public override returns (bool upkeepNeeded, bytes memory) {
        bool isOpen = RaffleState.OPEN == s_raffleState;
        bool timePassed = ((block.timestamp - s_lastTimeStamp) > i_interval);
        bool hasPlayers = s_players.length > 0;
        bool hasBalance = address(this).balance > 0;
        bool upKeepNeeded = isOpen && timePassed && hasPlayers && hasBalance;
    }
```

```solidity
    function performUpkeep(bytes calldata) external override {
        (bool upkeepNeeded, ) = checkUpkeep("");
        if (!upkeepNeeded) {
            revert Raffle__UpkeepNotNeeded(
                address(this).balance,
                s_players.length,
                uint256(s_raffleState)
            );
        }
        uint256 requestId = i_vrfCoordinator.requestRandomWords(
            i_gasLane,
            i_subscriptionId,
            REQUEST_CONFIRMATION,
            i_callBackGasLimit,
            NUM_WORDS
        );
        emit ReqestedRaffleWinner(requestId);
        s_raffleState = RaffleState.CLOSED;
        s_lastTimeStamp = block.timestamp;
    }
```
