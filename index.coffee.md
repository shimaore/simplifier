A tree is T ::= {value,children[c]: T}

    class T
      constructor: (@value,@children = {}) ->

      insert: (p,v) ->

        if p is ''
          if @value?
            throw new Error "Duplicate prefix"
          @value = v
          return

        c = p[0]
        @children[c] ?= new T()
        @children[c].insert (p.substr 1), v

DFS readout of the tree; the callback function is called with (key,value).

      output: (f,p = '') ->
        if @value?
          f p, @value

        return unless @children?

        for c,r of @children
          do (c,r) ->
            r.output f, p+c

      number_of_children: ->
        return 0 if not @children?
        Object.keys(@children).length

Build a new tree with a list of children values in each node.
A valued tree is S ::= {[value]: {count,children[c]: [S,S,..]}}
In other words transforms T ::= {value,children[c]: T} into S ::= {[value]: {count,children[c]: S}}

      values: ->

        if @value?
          @content = {}
          @content[@value] = count: 0, chars: []
          @content[@value].count++
        else
          @content = null

Prune node with no children

        if @number_of_children() < 1
          return

Complete the node by recursively evaluating each child.

        @content ?= {}

        for c,r of @children
          do (c,r) =>
            r.values()

Prune children with no values recorded.

            return if not r.content?

            for v,x of r.content
              do (v,x) =>

Prune empty children

                return if x.count < 1

                @content[v] ?= count: 0, chars: []
                @content[v].count += x.count
                @content[v].chars.push c

        return

      available_values: ->
        if @content?
          Object.keys @content
        else
          []

      migrate: (parent_value,agressive) ->

        return @migrated if @migrated?

        elected_value = @value ? parent_value

Agressive merging
-----------------

Agressive merging will fill upper nodes in the tree with values percolating from deeper nodes.

        if agressive
          cmp = (a,b) => @content[b].count - @content[a].count
          elected_value = @value ? @available_values().sort(cmp)[0]

Transform S ::= {[value]:{count,children[c]: S}} into T ::= {value,children[c]: T}

        if elected_value isnt parent_value
          r = new T elected_value
        else
          r = new T()

        for v,s of @content when v isnt elected_value
          for c in s.chars
            r.children[c] = @children[c].migrate elected_value, agressive

        @migrated = r

        return r

    module.exports = class Simplifier

      constructor: (@agressive) ->
        @root = new T()

      insert: (p,v) ->
        @root.insert p, v

      output: (f) ->
        @root.output f

Merge function, returns a suffix or infix and a new node or null;
- a single value if this node and all its descendants share the same value (including if it has no descendant);
- null if this node has no value and its descendants have no value (including if it has no descendant);
- false otherwise.

      merge: ->
        @root.values()
        @root = @root.migrate null, @agressive
