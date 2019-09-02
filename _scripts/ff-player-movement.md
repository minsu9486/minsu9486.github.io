---
title: "PlayerMovement.cs"
layout: splash
excerpt: "Floating Fuzzy"
---

***

~~~cs
using UnityEngine;
using UnitySampleAssets.CrossPlatformInput; // Mobile and PC (debug)

namespace CompleteProject
{
    public class PlayerMovement : MonoBehaviour
    {
        public GameObject body;
        public GameObject ShrinkParticlePref;

        Rigidbody2D playerRigidbody;
        Vector3 screenSize;

        Vector3 prevMovement;
        Vector3 movement;

        void Start()
        {
            Input.gyro.enabled = true;

            screenSize      = Camera.main.ScreenToWorldPoint(new Vector3(Screen.width, Screen.height, 0f));
            playerRigidbody = GetComponentInChildren<Rigidbody2D>();
        }

        void FixedUpdate ()
        {
            if (PlayerConfiguration.isDead)
                return;

            float directionX = 0; // CrossPlatformInputManager.GetAxisRaw("Horizontal");
            float directionY = 0; // CrossPlatformInputManager.GetAxisRaw("Vertical");

#if MOBILE_INPUT
            //// Input Ver. Gyroscope
            //Vector3 gyroRot       = Input.gyro.rotationRate;
            //Vector3 gyroRotUnbias = Input.gyro.rotationRateUnbiased;
            //Vector3 gravity       = Input.gyro.gravity;
            //Vector3 acceleration  = Input.gyro.userAcceleration;

            //if (0.01f > gravity.x && gravity.x > -0.01f)
            //    directionX = 0;
            //else
            //    directionX = gravity.x * speed * Time.deltaTime;

            //Debug.Log("(" + gyroRot.x + ", " + gyroRot.y + ", " + gyroRot.z + ")\n(" + gyroRotUnbias.x + ", " + gyroRotUnbias.y + ", " + gyroRotUnbias.z + ")\n(" + gravity.x + ", " + gravity.y + ", " + gravity.z + ")\n(" + acceleration.x + ", " + acceleration.y + ", " + acceleration.z + ")");

            // Input Ver. Touching
            if (Input.touchCount > 0)
            {
                Debug.Log("Touch");

                if (Input.GetTouch(Input.touchCount).phase.Equals(TouchPhase.Began)
                    || Input.GetTouch(Input.touchCount).phase.Equals(TouchPhase.Moved)
                    || Input.GetTouch(Input.touchCount).phase.Equals(TouchPhase.Stationary))
                {
                    Debug.Log("Touch");

                    // Touch left side
                    if (Input.mousePosition.x > Screen.width / 2f)
                        directionX = speed * Time.deltaTime;
                    // Thouch right side
                    else
                        directionX = -speed * Time.deltaTime;
                }
            }
// PC_INPUT for Debug Test
#else
            // By keyboard pressing
            directionX = CrossPlatformInputManager.GetAxisRaw("Horizontal");

            // By mouse clicking
            if (Input.GetMouseButtonDown(0) || Input.GetMouseButton(0))
            {
                // Click left side
                if (Input.mousePosition.x > Screen.width / 2f)
                    directionX =  PlayerConfiguration.playerSpeed * Time.deltaTime;
                // Click right side
                else
                    directionX = -PlayerConfiguration.playerSpeed * Time.deltaTime;
            }
#endif

            // Check the screen boundary
            {
                Transform bodyImage = body.transform.FindChild("Image_Body");

                // 0.85f is offset for the white space in png
                float radius = (bodyImage.GetComponent<SpriteRenderer>().sprite.bounds.max.x * bodyImage.localScale.x * 0.85f) * body.transform.localScale.x;
                float playerLeftBoundary = transform.position.x - radius;
                float playerRightBoundary = transform.position.x + radius;

                // Left Boundary of the Screen
                if (playerLeftBoundary <= -screenSize.x)
                {
                    if(directionX < 0)
                        directionX = 0;

                    transform.position = new Vector3(-screenSize.x + radius,
                        transform.position.y, transform.position.z);
                }
                // Right Boundary of the Screen
                else if (screenSize.x <= playerRightBoundary)
                {
                    if (directionX > 0)
                        directionX = 0;

                    transform.position = new Vector3(screenSize.x - radius,
                        transform.position.y, transform.position.z);
                }
            }

            // Move the player around the scene.
            Move (directionX, directionY);

            // If the player start to move along x-axis (left or right)
            if (prevMovement.x == 0 && directionX != 0)
            {
                if (body.transform.localScale.x > PlayerConfiguration.playerMinSize)
                {
                    // Shrink
                    body.transform.localScale = body.transform.localScale - new Vector3(PlayerConfiguration.playerShrinkVal, PlayerConfiguration.playerShrinkVal, 0f);

                    // The move sound effect : pitch by scale
                    // Start pitch        = 1.5
                    // Boundary size      = 1.25
                    // Offset coefficient = 0.2
                    transform.GetComponent<AudioSource>().pitch = 1.5f + ((1.25f - body.transform.localScale.x) * 0.1f);
                    transform.GetComponent<AudioSource>().Play();

                    // Particle
                    GameObject particleObj = Instantiate(ShrinkParticlePref) as GameObject;
                    particleObj.transform.position = transform.position;
                    particleObj.GetComponent<ParticleSystem>().startColor = body.transform.FindChild("Image_Body").GetComponent<SpriteRenderer>().color;
                    Destroy(particleObj, particleObj.GetComponent<ParticleSystem>().startLifetime);
                }
                else // if excess, Set to the minimum size
                    body.transform.localScale = new Vector3(PlayerConfiguration.playerMinSize, PlayerConfiguration.playerMinSize, 1.0f);
            }

            // Save the movement info
            prevMovement.Set(movement.x, movement.y, movement.z);
        }


        void Move (float x, float y)
        {
            // Set the movement vector based on the axis input.
            movement.Set(x, y, 0);
            movement = movement.normalized * PlayerConfiguration.playerSpeed * Time.deltaTime;

            // Move the player to it's current position plus the movement.
            transform.position = transform.position + movement;
        }
    }
}
~~~