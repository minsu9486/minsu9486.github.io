---
title: "PlayerCollision.cs"
layout: splash
excerpt: "Floating Fuzzy"
---

***

~~~cs
using UnityEngine;
using UnityEngine.UI; // Text
using MangoCompany; // Server (rank, score)

public class PlayerCollision : MonoBehaviour
{
    public GameObject counterText;
    public GameObject deadText;
    public GameObject deathParticle;
    public GameObject continueButton;
    public GameObject eatenSound;

    Counter counter;
    float playerSpriteSize;
    SpriteRenderer spriteBody;
    SpriteRenderer spriteFur;
    Transform TransEyes;

    void Start()
    {
        counter    = counterText.GetComponent<Counter>();
        spriteBody = transform.FindChild("Image_Body").GetComponent<SpriteRenderer>();
        spriteFur  = transform.FindChild("Image_Fur").GetComponent<SpriteRenderer>();
        TransEyes  = transform.FindChild ("Image_Eyes");
        playerSpriteSize = spriteBody.bounds.max.x;
    }

    void OnTriggerEnter2D(Collider2D other)
    {
        // Enemy
        if (other.CompareTag("Enemy"))
        {
            Transform otherBodyImage = other.transform.FindChild("Image_Body");

            float playerSize = playerSpriteSize * transform.localScale.x;
            // 0.85f is offset for the white space in png
            float otherSize = (otherBodyImage.GetComponent<SpriteRenderer>().sprite.bounds.max.x * otherBodyImage.localScale.x * 0.85f) * other.transform.localScale.x;

            // if the player eat the smaller fuzz
            if (playerSize > otherSize)
            {
                // Merge the two colors
                SpriteRenderer otherSpriteRenderer = otherBodyImage.GetComponent<SpriteRenderer>();
                Color combinedColor = new Color((spriteBody.color.r * 0.5f) + (otherSpriteRenderer.color.r * 0.5f),
                    (spriteBody.color.g * 0.5f) + (otherSpriteRenderer.color.g * 0.5f),
                    (spriteBody.color.b * 0.5f) + (otherSpriteRenderer.color.b * 0.5f));

                // Change the player's config
                spriteBody.color = combinedColor; // Body Color
                spriteFur.color = new Color(combinedColor.r, combinedColor.g, combinedColor.b, 1f); // Fur Color
                transform.localScale += new Vector3(PlayerConfiguration.playerGrowVal, PlayerConfiguration.playerGrowVal, 0); // Grow!
                counter.CountUp(); // Count up!

                // Scaling effect on the Player's eye
                transform.parent.GetComponent<ScaleEffect>().ActiveEffect(1f, 1.25f, 2.5f);

                // The growing sound effect : by player size
                GrowingSoundEffectBySize (transform.localScale.x);

                // The eaten enemy's effect
                GameObject particleDeath = other.transform.parent.FindChild("Particle_Death").gameObject;
                particleDeath.SetActive(true);
                particleDeath.transform.parent = transform.parent.parent;

                // Destroy the eaten enemy's object
                Destroy(other.transform.parent.gameObject);
            }
            else
            {
                GrowingSoundEffectBySize (other.transform.localScale.x); // by enemy size
                PlayerData.Instance.lastDeadType = PlayerData.DeadType.DT_BY_BIGGER;
                PlayerDead("by a bigger one");
            }
        }
        // Obstacle
        else if (other.CompareTag("Obstacle"))
        {
            GrowingSoundEffectBySize (0.5f); // by enemy size
            PlayerData.Instance.lastDeadType = PlayerData.DeadType.DT_BY_TRIANGLE;
            PlayerDead("by a pointed triangle");
        }
    }

    void GrowingSoundEffectBySize(float size)
    {
        // The growing sound effect : pitch by scale
        // Start pitch        = 1
        // Boundary size      = 1.25
        // Offset coefficient = 0.2
        eatenSound.GetComponent<AudioSource>().pitch = 1f + ((1.25f - size) * 0.2f);
        eatenSound.GetComponent<AudioSource>().Play();
    }
        
    // Show the dead screen with the given text
    void PlayerDead(string description)
    {
        // Active the dead particle effect
        deathParticle.SetActive(true);

        // Save the player's last config
        PlayerData.Instance.lastPlayedSize  = transform.localScale.x;
        PlayerData.Instance.lastPlayedColor = spriteBody.color;

        // Disable the player body
        PlayerConfiguration.isDead = true;
        gameObject.SetActive(false);

        // Dead Text
        {
            deadText.SetActive (true);

            // Fade in the dead text
            Text deadTitle  = deadText.GetComponent<Text> ();
            deadTitle.color = new Color (deadTitle.color.r, deadTitle.color.g, deadTitle.color.b, 0.01f);
            deadTitle.CrossFadeAlpha (75.0f, 1.0f, false);

            // Fade in the dead description
            Text deadDescription  = deadText.transform.FindChild ("Description").GetComponent<Text> ();
            deadDescription.text  = description;
            deadDescription.color = new Color (deadDescription.color.r, deadDescription.color.g, deadDescription.color.b, 0.01f);
            deadDescription.CrossFadeAlpha (75.0f, 1.0f, false);

            // Fade in the dead description
            Text deadTip  = deadText.transform.FindChild ("Tip").GetComponent<Text> ();
            deadTip.text  = TipData.Instance.GetTipByDeadType(PlayerData.Instance.lastDeadType, PlayerData.Instance.lastPlayedSize);
            deadTip.color = new Color (deadDescription.color.r, deadDescription.color.g, deadDescription.color.b, 0.01f);
            deadTip.CrossFadeAlpha (75.0f, 0.75f, false);
        }

        // Vibrating effect
        Handheld.Vibrate ();

        // Active the score board depends on the particle's life time
        Invoke("ActiveContinueButton", 1.5f);
    }

    void ActiveContinueButton()
    {
        continueButton.SetActive (true);

        // Fade in the continue button's text
        Text contiText = continueButton.transform.FindChild ("Text").GetComponent<Text> ();
        contiText.color = new Color (contiText.color.r, contiText.color.g, contiText.color.b, 0.01f);
        contiText.CrossFadeAlpha (75.0f, 1.0f, false);
    }

    public void ActiveScoreBoard()
    {
        // Disable the dead text
        Text text = deadText.GetComponent<Text>();
        text.color = new Color(text.color.r, text.color.g, text.color.b, 0.01f);
        deadText.SetActive(false);
        continueButton.SetActive (false);

        // Active the score board
        GameObject obj = GameObject.Find("PlayScreen");
        Transform scoreBoardTransform = obj.transform.FindChild("ScoreBoard");
        scoreBoardTransform.gameObject.SetActive(true);
    }
}
~~~