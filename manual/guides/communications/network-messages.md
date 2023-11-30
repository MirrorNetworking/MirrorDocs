# Network Messages

For the most part we recommend the high level [Commands and RPC](remote-actions.md) calls and [SyncVar](../synchronization/syncvars.md), but you can also send low level network messages. This can be useful if you want clients to send messages that are not tied to game objects, such as logging, analytics or profiling information.

There is a public interface called NetworkMessage that you can extend to make serializable network message structs. Serialize and Deserialize methods are automatically generated for network messages by Mirror, and they can efficiently handle any [supported mirror type](../data-types.md), but if you need to you can implement your own serialization methods by following our [serialization guide](../serialization.md). Some other useful tips for implementing low-level messages:

* Make sure all your members are public fields. Weaver doesn't serialize properties.
* If you need classes or complex containers such as `List<T>` and `Dictionary<T,K>`, you must implement the Read and Write methods yourself.

To send a message, use the `Send()` method on the NetworkClient, NetworkServer, and NetworkConnection classes, which all work the same way using a message struct that is derived from NetworkMessage. The code below demonstrates how to send and handle a message:

To declare a custom network message class and use it:

```csharp
using UnityEngine;
using Mirror;

public class Scores : MonoBehaviour
{
    public struct ScoreMessage : NetworkMessage
    {
        public int score;
        public Vector3 scorePos;
        public int lives;
    }

    public void SendScore(int score, Vector3 scorePos, int lives)
    {
        ScoreMessage msg = new ScoreMessage()
        {
            score = score,
            scorePos = scorePos,
            lives = lives
        };

        NetworkServer.SendToAll(msg);
    }

    public void SetupClient()
    {
        NetworkClient.RegisterHandler<ScoreMessage>(OnScore);
        NetworkClient.Connect("localhost");
    }

    public void OnScore(ScoreMessage msg)
    {
        Debug.Log("OnScoreMessage " + msg.score);
    }
}
```
