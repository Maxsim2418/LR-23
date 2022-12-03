# LR-23[LR-23.zip](https://github.com/Maxsim2418/LR-23/files/10146075/LR-23.zip)

Борнеман М.А

ЭВТ-70

Игровой движок:Unity

Лабораторная работа №23

Тема: разработка игрового проекта hophophop

Цель: приобрести навыки в создании проекта hophophop

Ход работы

1.	Выполнение работы 

1.	Разделил спрайты на несколько частей
 
![image](https://user-images.githubusercontent.com/119674602/205433767-a25fc67d-dbed-4dc6-81ca-2e13a14ff12e.png)

Рисунок 23.1 Разделённые спрайты

2.	Создал сцену

![image](https://user-images.githubusercontent.com/119674602/205433771-1e096154-cf56-4251-9b5f-326bcc8ddace.png)

Рисунок 23.2 – Сцена готового проекта

3.	Создал скрипт Player, отвечающий за поведение игрока.

Листинг Player.cs
using UnityEngine;

public class Player : MonoBehaviour
{
    public Vector2 jumpDirection;
    public int jumpForce;
    Rigidbody2D rgbd;
    Vector2 currentSpeed;
    public Vector2 maxSpeed;
    public bool takePlayerInput;
    Animator animator;
    GameManager gameManager;
    // Start is called before the first frame update
    void Start()
    {
        rgbd = GetComponent<Rigidbody2D>();
        rgbd.gravityScale = 0;
        animator = GetComponent<Animator>();
        gameManager = FindObjectOfType<GameManager>();
    }

    // Update is called once per frame
    void Update()
    {   if (takePlayerInput) return;
        if (Input.GetMouseButtonDown(0))
        {
            if (rgbd.gravityScale != 1) { rgbd.gravityScale = 1; }
            rgbd.AddForce(jumpDirection * jumpForce * Time.deltaTime);
            ControlSpeed();
        }
    }
    //control the speed of ball
    void ControlSpeed()
    {
        currentSpeed = rgbd.velocity;
        if (currentSpeed.x != maxSpeed.x) { currentSpeed.x = maxSpeed.x; }
        if (currentSpeed.y != maxSpeed.y) { currentSpeed.y = maxSpeed.y; }
        rgbd.velocity = currentSpeed;
    }
    public void PlayerDead()
    {

        //PlayDead Animation
        //Restart Scene
        Invoke("RestartScene", 2);
        animator.SetTrigger("Dead");
    }
    public void RestartScene()
    {
        gameManager.RestartScene();
    }

}

4.	Создал скрипт GameManager

Листинг GameManager.cs

using UnityEngine.SceneManagement;
using UnityEngine;

public class GameManager : MonoBehaviour
{
    // Start is called before the first frame update
    void Start()
    {
        
    }

  public void RestartScene()
    {
        SceneManager.LoadScene(SceneManager.GetActiveScene().buildIndex);
    }
}

5.	Создал скрипт Obstacle

Листинг Obstacle.cs

using UnityEngine;

public class Obstacle : MonoBehaviour
{
    // Start is called before the first frame update
    void Start()
    {
        
    }

    public void OnCollisionEnter2D(Collision2D collision)
    {
        if (collision.collider.tag == "Player")
        {
            FindObjectOfType<Player>().PlayerDead();
        }
    }
    public void RemoveObstacle()
    {
        //ball hit hoot remove it
        Destroy(gameObject);
    }
}

6.	Создал скрипт ObstacleHolder

Листинг ObstacleHolder.cs

using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class ObstacleHolder : MonoBehaviour
{
    Animator animator;
    // Start is called before the first frame update
    void Start()
    {
        animator = GetComponent<Animator>();
    }

    public void PlayDeadAnimation()
    {
        animator.SetTrigger("Destroy");
        Destroy(gameObject, 2f);
    }
}

7.	Создал скрипт ObjectHolderInstantiator

Листинг ObjectHolderInstantiator.cs

using UnityEngine;

public class ObjectHolderInstantiator : MonoBehaviour
{
    public GameObject obstacle;
    public int distanceBetweenObstacle;
     int currentObstaclePosition;
    public Vector2 minMaxYValue;
    // Start is called before the first frame update
    void Start()
    {
        InstantiateObstacle();
    }
    
    public void InstantiateObstacle()
    {
        for (int i = 0; i < 50; i++)
        {
            currentObstaclePosition += distanceBetweenObstacle;
            GameObject GO =  Instantiate(obstacle, new Vector3(currentObstaclePosition, 0, 0),
                Quaternion.identity) as GameObject;
            GO.transform.GetChild(1).transform.position = 
                new Vector2(GO.transform.GetChild(1).position.x, Random.Range(minMaxYValue.x, minMaxYValue.y));
        }
    }
}

8.	Создал скрипт ObjectHolderInstantiator

Листинг ObjectHolderInstantiator.cs

using UnityEngine;

public class ObjectHolderInstantiator : MonoBehaviour
{
    public GameObject obstacle;
    public int distanceBetweenObstacle;
     int currentObstaclePosition;
    public Vector2 minMaxYValue;
    // Start is called before the first frame update
    void Start()
    {
        InstantiateObstacle();
    }
    
    public void InstantiateObstacle()
    {
        for (int i = 0; i < 50; i++)
        {
            currentObstaclePosition += distanceBetweenObstacle;
            GameObject GO =  Instantiate(obstacle, new Vector3(currentObstaclePosition, 0, 0),
                Quaternion.identity) as GameObject;
            GO.transform.GetChild(1).transform.position = 
                new Vector2(GO.transform.GetChild(1).position.x, Random.Range(minMaxYValue.x, minMaxYValue.y));
        }
    }
}

9.	Создал скрипт HoopHolder

Листинг HoopHolder.cs

using UnityEngine;

public class HoopHolder : MonoBehaviour
{
    public Transform obstacle;
    bool obstacleCompleted;
    // Start is called before the first frame update
    void Start()
    {
        
    }
    public void OnCollisionEnter2D(Collision2D collision)
    {
        if (collision.collider.tag == "Player")
        {
            //take control of player
            FindObjectOfType<Player>().takePlayerInput = true;
        }
    }
    public void OnTriggerEnter2D(Collider2D collision)
    {
        if (obstacleCompleted) return;
        if (collision.tag == "Player")
        {
            FindObjectOfType<Player>().takePlayerInput = false;
            obstacle.GetComponent<Obstacle>().RemoveObstacle();
            DestroyGameObject();
        }

    }
    public void DestroyGameObject()
    {
        GetComponentInParent<ObstacleHolder>().PlayDeadAnimation();
        Destroy(gameObject);
    }
}

2.	Вывод

В ходе выполненной работы была создана игра hophophop
