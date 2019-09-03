---
title: "EnemyManager.cs"
permalink: /scripts/ff-enemy-manager.cs/
layout: splash
excerpt: "Floating Fuzzy"
---

***

~~~cs
using UnityEngine;

public class EnemyManager : MonoBehaviour
{
    public GameObject playScreen;
    Color backgroundColor;

    public GameObject player;
    Transform playerBody;

    // Smaller fuzz
    float feedSpawnTime;
    float feedTimer;

    // Bigger fuzz
    public GameObject enemyPref;
    float enemySpawnTime;
    float enemyTimer;

    // Obstacle
    public GameObject obstaclePref;
    float obstacleSpawnTime;
    float obstacleTimer;

    // For develop UI
    [HideInInspector]
    static public float sEneMinSize;
    [HideInInspector]
    static public float sEneMaxSize;
    [HideInInspector]
    static public float sEneSpawnFreq;
    [HideInInspector]
    static public float sEneSpeedMulti;

    [HideInInspector]
    static public float bEneMinSize;
    [HideInInspector]
    static public float bEneMaxSize;
    [HideInInspector]
    static public float bEneSpawnFreq;
    [HideInInspector]
    static public float bEneSpeedMulti;

    [HideInInspector]
    static public float triMinSize;
    [HideInInspector]
    static public float triMaxSize;
    [HideInInspector]
    static public float triSpawnFreq;
    [HideInInspector]
    static public float triSpeedMulti;

    // Init the all attrivutes of "level of difficulty"
    void Awake()
    {
        sEneMinSize    = 0.5f;
        sEneMaxSize    = 1.5f;
        sEneSpawnFreq  = 8.5f;
        sEneSpeedMulti = 1.5f;

        bEneMinSize    = 2.5f;
        bEneMaxSize    = 4.5f;
        bEneSpawnFreq  = 1.0f;
        bEneSpeedMulti = 0.35f;

        triMinSize     = 0.5f;
        triMaxSize     = 0.9f;
        triSpawnFreq   = 0.25f;
        triSpeedMulti  = 1.25f;
    }

    // Use this for initialization
    void Start ()
    {
        backgroundColor = GameObject.Find ("Environment/Background").GetComponent<SpriteRenderer> ().color;
        playerBody      = player.transform.FindChild("Object_Body");

        feedTimer = 0;
        feedSpawnTime = 1.5f / sEneSpawnFreq;

        enemyTimer = 0;
        enemySpawnTime = 1.5f / bEneSpawnFreq;

        obstacleTimer = 0;
        obstacleSpawnTime = 1.5f / triSpawnFreq;
    }
    
    // Update is called once per frame
    void Update ()
    {
        if (!playScreen.activeInHierarchy || PlayerConfiguration.isDead)
            return;

        FeedUpdate();
        EnemyUpdate();
        ObstacleUpdate();
    }
    
    // It creates a fuzz which can be a smaller one or a bigger one than the player
    GameObject CreateFuzz(GameObject fuzzObj, float playerScale)
    {
            GameObject fuzzObj = Instantiate(enemyPref, playScreen.transform) as GameObject;
            Transform fuzzBodyTransform = fuzzObj.transform.FindChild("Object_Body");
            EnemyMovement fuzzMove = fuzzObj.GetComponentInChildren<EnemyMovement>();
            Color fuzzColor = new Color(Random.Range(0.15f, 1f),Random.Range(0.15f, 1f), Random.Range(0.15f, 1f), 1.0f);
            float size = Random.Range(sEneMinSize, sEneMaxSize) * playerScale;

            fuzzBodyTransform.localScale = new Vector3(size, size, 1); // Size depend on Player's scale
            fuzzMove.velocity = fuzzMove.velocity / (size * sEneSpeedMulti); // Velocity
            fuzzColor = ChangeColorToBeNotSimilar (fuzzColor, backgroundColor);
            fuzzMove.body.transform.FindChild("Image_Body").GetComponent<SpriteRenderer>().color = fuzzColor;
            fuzzMove.body.transform.FindChild("Image_Fur").GetComponent<SpriteRenderer>().color  = fuzzColor;

            return fuzzObj;
    }

    // Smaller fuzz
    void FeedUpdate()
    {
        feedTimer += Time.deltaTime;
        if (feedTimer > feedSpawnTime)
        {
            // Create a smaller fuzz based on the player size
            float playerScale = playerBody.localScale.x;
            GameObject feedObj = CreateFuzz(Instantiate(enemyPref, playScreen.transform) as GameObject, playerScale)
            
            // Spown position
            feedObj.transform.position = new Vector3(Random.Range(-2.5f, 2.5f), Random.Range(-6f, -8f), 0f);

            feedTimer = 0;
            feedSpawnTime = 1.5f / sEneSpawnFreq;
        }
    }

    // Bigger fuzz
    void EnemyUpdate()
    {
        enemyTimer += Time.deltaTime;
        if (enemyTimer > enemySpawnTime)
        {
            // Create a bigger fuzz based on the player size
            float playerScale = playerBody.localScale.x;
            GameObject enemyObj = CreateFuzz(Instantiate(enemyPref, playScreen.transform) as GameObject, playerScale)

            // Spown position
            if (Random.Range(0, 1) == 1) // Random position
                enemyObj.transform.position = new Vector3(Random.Range(-2.5f, 2.5f), Random.Range(-6f, -8f), 0f);
            else // Spawn to the player's y axis to make the player move
                enemyObj.transform.position = new Vector3(player.transform.position.x, Random.Range(-6f, -8f), 0f);

            enemyTimer = 0;
            enemySpawnTime = 1.5f / bEneSpawnFreq;
        }
    }

    // Obstacle (small)
    void ObstacleUpdate()
    {
        obstacleTimer += Time.deltaTime;
        if (obstacleTimer > obstacleSpawnTime)
        {
            float playerScale = playerBody.localScale.x;

            float obstacleSize = Random.Range(triMinSize, triMaxSize) * playerScale;
            GameObject obstacleObj = Instantiate(obstaclePref, playScreen.transform) as GameObject;
            Transform obstacleBodyTransform = obstacleObj.transform.FindChild("Object_Body");
            ObstacleMovement obstacleMove = obstacleObj.GetComponentInChildren<ObstacleMovement>();
            Color obstacleColor = new Color(Random.Range(0.1f, 0.6f), Random.Range(0.1f, 0.6f), Random.Range(0.1f, 0.6f), 1.0f);
            obstacleColor = ChangeColorToBeNotSimilar (obstacleColor, backgroundColor);

            // Spown position
            obstacleObj.transform.position = new Vector3(Random.Range(-2.5f, 2.5f), Random.Range(-6f, -8f), 0f);
            // Size depend on Player's scale
            obstacleBodyTransform.localScale = new Vector3(obstacleSize, obstacleSize, 1);

            obstacleMove.velocity = obstacleMove.velocity / (obstacleSize * triSpeedMulti);
            obstacleMove.body.transform.FindChild("Image_Body").GetComponent<SpriteRenderer>().color = obstacleColor;

            obstacleTimer = 0;
            obstacleSpawnTime = 1.5f / triSpawnFreq;
        }
    }

    // Objects should be easly seen in a colored background 
    Color ChangeColorToBeNotSimilar(Color destination, Color source)
    {
        Color gap = source - destination;

        if (Mathf.Abs (gap.r) < 0.075f)
            destination.r = (0.075f - Mathf.Abs (gap.r)) * 10f;

        if (Mathf.Abs (gap.g) < 0.075f)
            destination.g = (0.075f - Mathf.Abs (gap.g)) * 10f;

        if (Mathf.Abs (gap.b) < 0.075f)
            destination.b = (0.075f - Mathf.Abs (gap.b)) * 10f;

        return destination;
    }

    public void DeleteAllObjects()
    {
        for (int i = 3; i < playScreen.transform.childCount; ++i)
            Destroy(playScreen.transform.GetChild(i).gameObject);
    }
}
~~~