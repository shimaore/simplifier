    chai = require 'chai'
    chai.should()

    describe 'The simplifier', ->
      Simplifier = require '..'

      it 'should work with single value', ->
        s = new Simplifier()
        s.insert '1', 'un'
        s.merge()
        I = {}
        s.output (k,v) ->
          I[k] = v
        I.should.have.property '1', 'un'

      it 'should work with two values', ->
        s = new Simplifier()
        s.insert '1', 'un'
        s.insert '2', 'deux'
        s.merge()
        I = {}
        s.output (k,v) ->
          I[k] = v
        I.should.have.property '1', 'un'
        I.should.have.property '2', 'deux'

      it 'should require simple value', ->
        s = new Simplifier()
        s.insert '1', 'un'
        s.insert '2', 'deux'
        s.insert '22', 'vingt-deux'
        s.merge()
        I = {}
        s.output (k,v) ->
          I[k] = v
        I.should.have.property '1', 'un'
        I.should.have.property '2', 'deux'
        I.should.have.property '22', 'vingt-deux'

      it 'should simplify identical values', ->
        s = new Simplifier()
        s.insert '336', 'one'
        s.insert '3366', 'one'
        s.insert '336677', 'one'
        s.insert '336678', 'two'
        s.merge()
        I = {}
        s.output (k,v) ->
          I[k] = v
        I.should.not.have.property ''
        I.should.not.have.property '3'
        I.should.not.have.property '33'
        I.should.not.have.property '33667'

        I.should.have.property '336', 'one'
        I.should.not.have.property '3366'
        I.should.not.have.property '336677'
        I.should.have.property '336678', 'two'

      it 'should handle deep values', ->
        s = new Simplifier()
        s.insert '', 'one'
        s.insert '336677', 'one'
        s.merge()
        I = {}
        s.output (k,v) ->
          I[k] = v
        I.should.have.property '', 'one'
        I.should.not.have.property '3'
        I.should.not.have.property '33'
        I.should.not.have.property '336'
        I.should.not.have.property '3366'
        I.should.not.have.property '33667'
        I.should.not.have.property '336677'

      it 'should handle deep, more specific values', ->
        s = new Simplifier()
        s.insert '3', 'one'
        s.insert '336677', 'two'
        s.merge()
        I = {}
        s.output (k,v) ->
          I[k] = v
        I.should.not.have.property ''
        I.should.have.property '3', 'one'
        I.should.not.have.property '33'
        I.should.not.have.property '336'
        I.should.not.have.property '3366'
        I.should.not.have.property '33667'
        I.should.have.property '336677', 'two'

      it 'should handle same-level values', ->
        s = new Simplifier()
        s.insert '3', 'one'
        s.insert '3456', 'one'
        s.insert '3765', 'two'
        s.insert '4', 'two'
        s.insert '4456', 'one'
        s.insert '4765', 'two'
        s.merge()
        I = {}
        s.output (k,v) ->
          I[k] = v
        I.should.not.have.property ''
        I.should.have.property '3', 'one'
        I.should.not.have.property '3456'
        I.should.have.property '3765', 'two'
        I.should.have.property '4', 'two'
        I.should.have.property '4456', 'one'
        I.should.not.have.property '4765'

      it 'should merge agressively', ->
        s = new Simplifier true
        s.insert '1201', 'one'
        s.insert '1202', 'one'
        s.insert '1203', 'one'
        s.insert '1213', 'one'
        s.insert '1256', 'one'
        s.insert '1356', 'one'
        s.insert '1782', 'one'
        s.insert '1888', 'one'
        s.insert '1999', 'two'
        s.insert '33', 'three'
        s.merge()
        I = {}
        s.output (k,v) ->
          I[k] = v
        I.should.have.property '', 'one'
        I.should.have.property '19', 'two'
        I.should.not.have.property '1201'
        I.should.not.have.property '1202'
        I.should.not.have.property '1203'
        I.should.not.have.property '1213'
        I.should.not.have.property '1256'
        I.should.not.have.property '1356'
        I.should.not.have.property '1782'
        I.should.not.have.property '1888'
        I.should.not.have.property '1999'
        I.should.not.have.property '33'
