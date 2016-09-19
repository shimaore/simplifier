Simplify routing tables
-----------------------

Build new routing tables (with matching semantics) by folding duplicate routes.

    var Simplifier = require('simplifier')

    var tree = new Simplifier()

    tree.insert('33','carrier: one')
    tree.insert('3367','carrier: one')
    tree.insert('3377','carrier: two')
    tree.insert('33778','carrier: two')

    tree.merge()
    var table = {}
    tree.output( function(prefix,route){
      table[prefix] = route
    })

    // table = { '33':'carrier: one', '3377', 'carrier: two' }

Routes and prefixes must both be strings.
