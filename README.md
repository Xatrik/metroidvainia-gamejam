# metroidvainia-gamejam
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class Player : MonoBehaviour
{
    public ParticleSystem jumpdust;
    public ParticleSystem dashdust;
    public ParticleSystem walkdust;
    public float speed;
    public Rigidbody2D rb;
    private bool _isFacingRight = true;
    public Animator anim;
    public BoxCollider2D col;
    public CompositeCollider2D platforms;
    public float jumpforce;
    public Rigidbody2D Bullet;
    public Transform firepoint;
    public BoxCollider2D damage;
    public Transform respawn;
    public float dashdistance = 15f;
    public bool candash = false;
    private bool isdashing;
    float doubletaptime;
    KeyCode lastkeycode;
    
    // Start is called before the first frame update
    void Start()
    {
        
    }

    // Update is called once per frame
    void Update()
    {
        float inputx = Input.GetAxisRaw("Horizontal") * speed;

        if(inputx != 0 && col.IsTouching(platforms))
        {
            Walkdust();
        }

        if(isdashing != true)
        {
            rb.velocity = new Vector2(inputx, rb.velocity.y);            
        }

        anim.SetFloat("isMoving?", Mathf.Abs(inputx));

        if(_isFacingRight && inputx < 0)
        {
            Flip();
        }else if(!_isFacingRight && inputx > 0)
        {
            Flip();
        }
        if (col.IsTouching(platforms))
        {
        if(Input.GetKeyDown(KeyCode.UpArrow))
        {
            Jumpdust();
            rb.velocity = new Vector2(rb.velocity.x, jumpforce);
        }
        }
        if(Input.GetKeyDown(KeyCode.DownArrow))
        {
            float angle = _isFacingRight ? 0f : 180f;
            Rigidbody2D bulletclone = (Rigidbody2D) Instantiate( Bullet, firepoint.position, Quaternion.Euler(new Vector3(0, 0, angle)));
        }
        if(col.IsTouching(damage))
        {
            transform.position = respawn.position;
        }

        if(candash == true || isdashing != true)
        {
            dashingleft();
            dashingright();
        }
        
    }
    void dashingright()
    {
        if(Input.GetKeyDown(KeyCode.RightArrow) || Input.GetKeyDown(KeyCode.D))
        {
            if(doubletaptime > Time.time && lastkeycode == KeyCode.D)
            {
                StartCoroutine(dash(1f));
            }else
            {
                doubletaptime = Time.time + 0.5f;
            }
            lastkeycode = KeyCode.D;
        }
    }
        void dashingleft()
    {
        if(Input.GetKeyDown(KeyCode.LeftArrow) || Input.GetKeyDown(KeyCode.A))
        {
            if(doubletaptime > Time.time && lastkeycode == KeyCode.A)
            {
                StartCoroutine(dash(-1f));
            }else
            {
                doubletaptime = Time.time + 0.5f;
            }
            lastkeycode = KeyCode.A;
        }
    }
    void Flip()                     //flips player to the direction it is walking in
    {
        _isFacingRight = !_isFacingRight;
        transform.Rotate(0.0f, -180f, 0.0f);
    }
    IEnumerator dash (float direction)
    {
        transform.Rotate(new Vector3(0, 0, 90 * direction)) ;
        Dashdust();
        isdashing = true;
        rb.velocity = new Vector2(rb.velocity.x, 0f);
        rb.AddForce(new Vector2(dashdistance * direction, 0f), ForceMode2D.Impulse);
        float gravity = rb.gravityScale;
        rb.gravityScale = 0f;
        yield return new WaitForSeconds(0.4f);
        transform.Rotate(new Vector3(0, 0, -90 * direction)) ;
        isdashing = false;
        rb.gravityScale = gravity;

    }
    void Dashdust()
    {
        dashdust.Play();
    }
    void Walkdust()
    {
        walkdust.Play();
    }
    void Jumpdust()
    {
        jumpdust.Play();
    }
    void OnTriggerEnter2D(Collider2D other)
    {
        if(other.gameObject.tag == "powerup")
        {
            candash = true;
            Destroy(other.gameObject);
        }
    }
}
