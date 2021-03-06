## Як працуе `this`

У JavaScript `this` азначае канцэптуальна іншую рэч, чым у іншых мовах праграмавання.
Існуе роўна **пяць** варыянтаў таго, да чаго можа быць прывязана `this`.

### Глабальна зона бачнасці

    this;

Калі `this` выкарыстоўваецца ў глабальнай зоне бачнасці - яна спасылаецца на *глабальны* аб'ект


### Выклік функцыі

    foo();

Тут, `this` усё яшчэ спасылаецца на *глабальны* аб'ект.

> **Заўвага для ES5:** У строгім рэжыме, паняцця глабальнасці **больш не існуе**.
> `this` у такім выпадку будзе мець значэнне `undefined`.

### Выклік метада

    test.foo();

У гэтым выпадку, `this` будзе спасылацца на аб'ект `test`.

### Выклік канструктара

    new foo();

Выклік функцыі, перад якім прысутнічае ключавое слова `new`, выступае ў якасці
[канструктара](#function.constructors). Унутры функцыі, `this` будзе спасылацца
на *новаствораны* аб'ект.

### Яўная ўстаноўка `this`

    function foo(a, b, c) {}

    var bar = {};
    foo.apply(bar, [1, 2, 3]); // масіў разгорнецца ў ніжэйпрыведзенае
    foo.call(bar, 1, 2, 3); // вынікам будзе a = 1, b = 2, c = 3

Пры выкарыстоўванні метадаў `call` або `apply` з `Function.prototype`, значэнню
`this` унутры выкліканай функцыі **яўна прысвойваецца** значэнне першага
аргумента адпаведнага выкліка функцыі.

Як вынік, у вышэйпрыведзеным прыкладзе *правіла метада* **не** працуе, і `this`
унутры `foo` будзе мець значэнне `bar`.

> **Заўвага:** `this` **не можа** быць выкарыстаны як спасылка ўнутры літэрала
> `Object`. Таму ў кодзе `var obj = {me: this}` `me` **не** будзе спасылацца на
> `obj`, бо `this` прывязваецца толькі па пяці вышэйапісаных правілах.

### Магчымыя пасткі

Не гледзячы на тое, што большасць гэтых прыкладаў лагічныя, першы можна лічыць
яшчэ адным недаглядам мовы, бо ён **ніколі** не мае практычнага прымянення.

    Foo.method = function() {
        function test() {
            // this спасылаецца на глабальны аб'ект
        }
        test();
    }

Памылковым меркаваннем будзе тое, што `this` унутры `test` будзе спасылацца на
`Foo`; Але на самрэч **гэта не так**.

Каб атрымаць доступ да `Foo` з цела `test`, вы можаце стварыць лакальную
пераменную унутры `метада` што будзе спасылацца на `Foo`.

    Foo.method = function() {
        var self = this;
        function test() {
            // Тут выкарыстоўвайце self замест this
        }
        test();
    }

`self` гэта звычайнае імя пераменнай, але яно часта выкарыстоўваецца для спасылкі
на знешні `this`. У камбінацыі з [замыканнямі](#function.closures), яно можа быць
выкарыстана для перадачы `this` навокал.

У ECMAScript 5 можна выкарыстаць метад `bind` у камбінацыі з ананімнай функцыяй,
дзеля таго каб атрымаць аналагічны вынік.

    Foo.method = function() {
        var test = function() {
            // this цяпер спасылаецца на Foo
        }.bind(this);
        test();
    }

### Прысвойванне метадаў

Яшчэ адна рэч якая **не** працуе ў JavaScript - гэта стварэнне псэўданімаў функцый,
то бок **прысвойванне** значэння метада пераменнай.

    var test = someObject.methodTest;
    test();

Паводле першага правіла, `test` цяпер працуе як звычайны выклік функцыі;
адпаведна, `this` унутры больш не будзе спасылацца на `someObject`.

Поздняе звязванне `this` можа падацца дрэннай ідэяй, але насамрэч якраз дзякуючы
гэтаму працуе [спадкаемства прататыпаў](#object.prototype).

    function Foo() {}
    Foo.prototype.method = function() {};

    function Bar() {}
    Bar.prototype = Foo.prototype;

    new Bar().method();

Калі выклікаецца `method` новага экзэмпляра `Bar`, `this` будзе спасылацца на гэты
экзэмпляр.
