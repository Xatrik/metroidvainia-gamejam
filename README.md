# metroidvainia-gamejam
player script
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class Player : MonoBehaviour
{
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
    public BoxCollider2D powerup1;
    public float dashdistance = 15f;
    public bool candash = false;
    private bool isdashing;
    
    // Start is called before the first frame update
    void Start()
    {
        
    }

    // Update is called once per frame
    void Update()
    {
        float inputx = Input.GetAxisRaw("Horizontal") * speed;

        rb.velocity = new Vector2(inputx, rb.velocity.y);

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
        if(col.IsTouching(powerup1))
        {
            candash = true;
            Destroy(powerup1.gameObject);
        }
        if(candash == true)
        {

        }
        
    }

    void Flip()                     //flips player to the direction it is walking in
    {
        _isFacingRight = !_isFacingRight;
        transform.Rotate(0.0f, -180f, 0.0f);
    }
       
}
