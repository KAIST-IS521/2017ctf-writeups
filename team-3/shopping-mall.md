shopping-mall Writeup
===========

# Before read...
I found vulnerability on `/product/purchase` page by hand-fuzzing during the CTF.

But when I tested my payload on my VM. It occured SQL error on MySQL.
So I can't analyze vulnerability perfectly.

# Abstract
There were two vulnerabilities on the server.
Type confusion vulnerability and SQL injection(maybe?) on `/product/purchase` page.

Vulnerability was so simple. When I purchase the 'FLAG' directly on main(`/#product`) page.
I inserted single quote in product_num. And 'FLAG' was bought.

And I read code again because to check vulnerability.

## Type confusion on `/product/purchase` page
### Describe
First it has a type confusion vulnerablity when check product_num value.
And insert invalid SQL query with product_num.

### Source code
```js
// product.js

router.get('/purchase', function(req, res, next)
{
    var product_id = req.param('product-id');
    var product_num = req.param('product-num');

    if ( product_num < 1 || 8 < product_num ) { // type confusion, "'" is valid number
        res.json( { status: 0, message: 'Not valid input...' } );
        return;
    }

    // omitted...

    var item_iString = 'INSERT INTO order_items SET ? ';
    var item_p = { order_id: order_id, product_id: product_id, product_num: product_num };

    // omitted...

    q.query( item_iString, item_p, function( err, result, fields ) // It successfully inserted query on CTF. But error has occured on my VM.
    {

      // omitted...

    });
});
```

And then server runs check_transaction.py script to check that user sended amount of money.
In this process, because of the invalid `product_num`. Transaction check returns success.

```js
// bank.js

bank.connectBank = function( )
{
    // SQL query for searching all the pending status orders
    var sString =  'SELECT orders.order_id AS id, orders.added_time AS time, orders.bank_account AS account, orders.bank_pw AS pw, SUM( order_items.product_num * products.price ) AS amount FROM ( orders JOIN order_items ) JOIN products ON ( orders.order_id = order_items.order_id ) AND ( order_items.product_id = products.product_id ) WHERE orders.status = "pending" GROUP BY orders.order_id ';

    q.query( sString, function( err, result, fields )
    {
        var items = result;
        items.forEach( function( item, index )
        {
            // omitted...

            var check_result = cp.execSync( config.check_transaction + ' ' + ipaddr + ' ' + item.account + ' ' + item.pw + ' ' + item.amount ); // Maybe item.amount will be 0 when SQL injection is successed.

            if (check_result.toString().substring(0,7) == 'success')
            {
                var uString = 'UPDATE orders SET status = "completed" WHERE order_id = ? ';

                q.query( uString, [ item.id ], function( err, result, fields )
                {
                    return;
                });
            }

            // omitted...

        });
    });
};
```

### Payload
`curl http://mall.teamx/product/purchase?prooduct-id=3&product-num='`

# Summary
Check valid type when compare number on javascript.
And use program of latest version!
