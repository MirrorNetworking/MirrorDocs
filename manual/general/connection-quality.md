# Connection Quality

Mirror does a lot of work to smooth out poor network connections.

However, there's only so much we can do on a poor connection.

Best practice for poor connections is to simply display a 'poor connection' indicator.

\=> This removes the magic and makes it very obvious why a player might be lagging.



Mirror's Connection Quality comes in three parts.

**ConnectionQuality.cs**: standalone connection quality heuristics, which can be used outside of Unity if needed. Currently it offers two heuristics:&#x20;

* **Simple** (based on Ping & Jitter)&#x20;
* **Pragmatic** (based on Snapshot Interpolation).

**NetworkManager Callbacks**

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
