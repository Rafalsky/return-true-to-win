# return-true-to-win
Valid answers for alf.nu/ReturnTrue game

### id
```
function id(x) {
    return x;
}
```
```
id(!0);
```

### reflexive
```
function reflexive(x) {
    return x != x;
}
```
```
reflexive(NaN);
```

### transitive
```
function transitive(x,y,z) {
    return x && x == y && y == z && x != z;
}
```
```
transitive([],0,[]);
```


### counter
```
function counter(f) {
    var a = f(), b = f();
    return a() == 1 && a() == 2 && a() == 3
        && b() == 1 && b() == 2;
}
```
```
counter((_=1)=>$=>_++);
```

### peano
```
function peano(x) {
    return (x++ !== x) && (x++ === x);
}
```
```
peano(2**53-1);
```

### array
```
function array(x,y) {
    return Array.isArray(x) && !(x instanceof Array) &&
          !Array.isArray(y) &&  (y instanceof Array);
}
```
```
array(Array.prototype,{__proto__:Array.prototype});
```

### instance
```
function instance(x,y) {
  return x instanceof y && y instanceof x && x !== y;
}
```
```
instance(Object,Proxy);
```

### proto1
```
function proto1(x) {
    return x && !("__proto__" in x);
}
```
```
proto1({__proto__:null});
```
### undef
```
function undef(x) {
    return !{ undefined: { undefined: 1 } }[typeof x][x];
}
```
```
undef(document.all);
```

### symmetric
```
function symmetric(x,y) {
    return x == y && y != x;
}
```
```
symetric(n=1,{valueOf:$=>n++});
```

### ouroborobj
```
function ouroborobj(x) {
    return x in x;
}
```
```
ouroborobj([0]);
```
### truth
```
function truth(x) {
    return x.valueOf() && !x;
}
```
```
truth((String.prototype.valueOf=(_=>true),''));
```

### wat
```
function wat(x) {
    return x('hello') == 'world:)' && !x;
}
```
```
wat(d=document.all,d(0).id='hello',d(0).toString=_=>'world:)');
```

### evil1
```
var eval = window.eval;
function evil1(x) {
    return eval(x+'(x)') && !eval(x)(x);
}
```
```
evil1(_=>0);
```

### evil2
```
var eval = window.eval;
function evil2(x) {
    return eval('('+x+')(x)') && !eval(x)(x);
}
```
```
evil2(_=>x=_=>0);
```

### evil3
```
var eval = window.eval;
function evil3(parameter) {
    return eval('('+parameter+')(parameter)') && 
          !eval(parameter)(parameter);
}
```


### random1
```
function random1(x) {
    return Math.random() in x;
}
```
```
random1([Math.random=_=>0]);
```

### random2
```
var rand = Math.random();
function random2(x) {
  return rand in x;
}
```
```
random2(new Proxy({},{has:_=>1}));
```

### random3
```
var key = crypto.getRandomValues(new Uint32Array(4));
function random3(x) {
    var d = 0;
    for (var i=0; i<key.length; i++) {
        d |= key[i] ^ x[i];
    }
    return d === 0;
}
```
```
random3(Uint32Array.prototype.__proto__={});
```

### random4
```
var rand = Math.random();
function random4(x) {
    return rand === x;
}
```

### letsgo
```
function letsgo(x) {
    let a = let `abc`;
    return `abc` === a;
}
```
```
letsgo(let=s=>s[0]);
```

### closure
```
function closure(x) {
    return x[x] == x;
}
```

```
closure([0]);
```

### native
```
const toString = Function.prototype.toString;
function native(x) {
  return (x() === 1) && (x.toString() === 'function () { [native code] }') && (toString.call(x) === x.toString())
}
```
```
native(function(){return 1}.bind(null));
```

### infinity
```
function infinity(x, y) {
    return x === y && 1/x < 1/y 
}
```

### stringable
```
function stringable(s) {
    try {
      '' + s;
    } catch (e) {
      return String(s) == s.toString();
    }
}
```
```
stringable(Symbol());
```

### thraw
```
function thaw(unfreeze) {
    var obj = {x: 1};
    Object.freeze(obj);
    unfreeze(obj);
    return obj.x !== 1;
}
```

### thraw2
```
var freeze = Object.freeze;

function thaw2(unfreeze, k) {
    var obj = {};
    obj[k] = 1;
    freeze(obj);
    unfreeze(obj);
    return obj[k] !== 1;
}
```

### thraw3
```
var freeze = Object.freeze;

function thaw3(unfreeze, obj) {
    obj.x = 1;
    freeze(obj);
    unfreeze(obj);
    return 'x' in obj && obj.x !== 1;
}
```

### calc
```
// Input must be a string; no redefinition shenanigans this time.
verifyInput = JSON.parse;

// Reviewer didn't like eval(), so parse the expression by hand for safety.
// Use:
//    a = 1 + 1;
//    result = sqrt(a + PI);
function calc(s) {
    var tokens = s.match(/(\w+|[+=();])/g);
    var vars = {
        // Expose only safe functions
        PI: Math.PI,
        sqrt: Math.sqrt,
        isNaN: Math.isNaN
    };
    var peek = _ => tokens[0];
    var eat = _ => tokens.shift();
    var ate = x => peek() === x && eat();
    var want = x => { if (!ate(x)) throw 'Expected "'+x+'" at '+JSON.stringify(tokens); }
    function statement() {
        vars[eat()] = [want('='), expr(), want(';')][1];
    }
    function expr() {
        for (var v = term(); ate('+'); v = v + term());
        return v;
    }
    function term() {
        for (var v = atom(); ate('('); v = v([expr(), want(')')][0]));
        return v;
    }
    function atom() {
        var p = eat(), n = parseInt(p);
        if (!isNaN(n)) return n;
        if (!(p in vars)) throw 'Undefined '+p;
        return vars[p];
    }
    while (peek()) statement();
    return vars.result;
}
```

### calc2
```
// Input must be a string; no redefinition shenanigans this time.
verifyInput = JSON.parse;

// Reviewer didn't like eval(), so parse the expression by hand for safety.
// Use:
//    a = 1 + 1;
//    result = sqrt(a + PI);
function calc2(s) {
    var tokens = s.match(/(\w+|[+=();])/g);
    var vars = {
        // Expose only safe functions
        PI: Math.PI,
        sqrt: Math.sqrt
    };
    var peek = _ => tokens[0];
    var eat = _ => tokens.shift();
    var ate = x => peek() === x && eat();
    var want = x => { if (!ate(x)) throw 'Expected "'+x+'" at '+JSON.stringify(tokens); }
    function statement() {
        vars[eat()] = [want('='), expr(), want(';')][1];
    }
    function expr() {
        for (var v = term(); ate('+'); v = v + term());
        return v;
    }
    function term() {
        for (var v = atom(); ate('('); v = v([expr(), want(')')][0]));
        return v;
    }
    function atom() {
        var p = eat(), n = parseInt(p);
        if (!isNaN(n)) return n;
        if (!(p in vars)) throw 'Undefined '+p;
        return vars[p];
    }
    while (peek()) statement();
    return result;
}
```

### total
```
function total(x) {
    return (x < x) && (x == x) && (x > x);
}
```
```
total({valueOf:_=>n--%2},n=2);
```
