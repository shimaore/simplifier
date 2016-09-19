Specifications
--------------

    test_sets = [
    ]

    for test_set in test_sets

      describe test_set.name, ->

Provided a set A = { (p<sub>a</sub>,v<sub>a</sub>) } of (prefix,value) pairs, we apply P = `insert(p,v)` which incrementally builds a set I = { (p<sub>i</sub>,v<sub>i</sub>) } from the values in A.

        I = new Simplifier()
        before = ->
          A = test_set.pairs
          for pair in A
            I.insert pair.prefix, pair.value

If we define p<sub>1</sub> < p<sub>2</sub> as p<sub>1</sub> is a prefix (strict) of p<sub>2</sub>,
we verify the following properties:

- ∀ i,j, p<sub>i</sub> ≠ p<sub>j</sub>

        it '∀ i,j, p_i ≠ p_j', (done) ->

  Enforced by rejecting duplicate insertion and retrieving based on the prefix.

          E = new Simplifier()
          try
            E.insert '1', true
            E.insert '2', true
            E.insert '1', false
          catch error
            done()

- ∀ i,j, p<sub>i</sub> < p<sub>j</sub> ⇒ v<sub>i</sub> ≠ v<sub>j</sub>

        it '∀ i,j, p_i < p_j ⇒ v_i ≠ v_j', (done) ->

- insert(p<sub>a</sub>,v<sub>a</sub>) ⇒ ∃ p<sub>i</sub> ≤ p<sub>a</sub> ∣ v<sub>i</sub> = v<sub>a</sub> ∧ ¬(∃ p<sub>j</sub>, p<sub>i</sub> < p<sub>j</sub> ∣ v<sub>j</sub> ≠ v<sub>a</sub>)

- |A| ≥ |I|
