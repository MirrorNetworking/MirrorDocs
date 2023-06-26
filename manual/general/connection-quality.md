# Connection Quality

Mirror does a lot of work to smooth out poor network connections.

However, there's only so much we can do on.

{% hint style="warning" %}
For poor connections, it's best practice to display a '**Poor Connection**' warning.
{% endhint %}

Mirror's ConnectionQuality consists of three parts to make this very easy for your game.

### **ConnectionQuality.cs**

Standalone connection quality enum & heuristics, which can be used outside of Unity if needed.

Mirror provides the following connection quality levels:

```csharp
public enum ConnectionQuality : byte
{
    EXCELLENT,  // ideal experience for high level competitors
    GOOD,       // very playable for everyone but high level competitors
    FAIR,       // very noticeable latency, not very enjoyable anymore
    POOR,       // unplayable
    ESTIMATING, // still estimating
}
```

Currently it offers two heuristics:&#x20;

* **Simple** (based on Ping & Jitter)&#x20;
* **Pragmatic** (based on Snapshot Interpolation).

### NetworkPingDisplay

This component can be added to the NetworkManager to display a Ping & Connection Quality indicator on the bottom right of the screen. Feel free to modify this to your needs, or create your own.

<figure><img src="../../.gitbook/assets/2023-06-25 - connection quality, gui, callback.png" alt=""><figcaption></figcaption></figure>

### **NetworkManager Callbacks**

*   **CalculateConnectionQuality()** can be overwritten to inject your own heuristic. By default, it uses the **Simple** heuristic from above. This is called every **connectionQualityInterval** seconds, which can also be configured in the NetworkManager.



    ```csharp
    protected virtual void CalculateConnectionQuality()
    {
        NetworkClient.connectionQuality = ConnectionQualityHeuristics.Simple(
            NetworkTime.rtt, 
            NetworkTime.rttVar
        );
    }
    ```
*   **OnConnectionQualityChanged**() can be used to show warnings to the user. By default, this emits a log message - useful for debugging user logs, etc.



    ```csharp
    protected virtual void OnConnectionQualityChanged(ConnectionQuality previous, ConnectionQuality current)
    {
        Debug.Log($"[Mirror] Connection Quality changed from {previous} to {current}");
    }
    ```
