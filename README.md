# Clean Code PHP

## Sumário

  1. [Introdução](#introdução)
  2. [Variáveis](#variáveis)
     * [Use variáveis pronunciaveis e com significado claro](#use-variáveis-pronunciaveis-e-com-significado-claro)
     * [Use o mesmo vocabulário para o mesmo tipo de variável](#use-o-mesmo-vocabulário-para-o-mesmo-tipo-de-variável)
     * [Use nomes pesquisáveis (parte 1)](#use-nomes-pesquisáveis-parte-1)
     * [Use nomes pesquisáveis (parte 2)](#use-nomes-pesquisáveis-parte-2)
     * [Use nomes explicativos](#use-nomes-explicativos)
     * [Evite mapa mental](#evite-mapa-mental)
     * [Não coloque contexto desnecessário](#não-coloque-contexto-desnecessário)
     * [Use argumentos padrão ao invéis de condicionais](#use-argumentos-padrão-ao-invéis-de-condicionais)
  3. [Funções](#funções)
     * [Parâmetros de funções (2 ou menos)](#parâmetros-de-funções-2-ou-menos)
     * [Funções devem fazer apenas uma coisa](#funções-devem-fazer-apenas-uma-coisa)
     * [Nome de função deve dizer o que ela faz](#nome-de-função-deve-dizer-o-que-ela-faz)
     * [Funções devem ter apenas um nível de abstração](#funções-devem-ter-apenas-um-nível-de-abstração)
     * [Não use flags como parâmetros](#não-use-flags-como-parâmetros)
     * [Evite efeito colateral](#evite-efeito-colateral)
     * [Não escreva funções globais](#não-escreva-funções-globais)
     * [Não use o padrão Singleton](#não-use-o-padrão-singleton)
     * [Encapsule condicionais](#encapsule-condicionais)
     * [Evite condicionais negativas](#evite-condicionais-negativas)
     * [Evite condicionais](#evite-condicionais)
     * [Evite verificação de tipo (parte 1)](#evite-verificação-de-tipo-parte-1)
     * [Evite verificação de tipo (parte 2)](#evite-verificação-de-tipo-parte-2)
     * [Remova código morto](#remova-código-morto)
  4. [Objetos e estrutura de dados](#objetos-e-estrutura-de-dados)
     * [Use getters e setters](#use-getters-e-setters)
     * [Faça objetos terem membros private/protected](#faça-objetos-terem-membros-privateprotected)
  5. [Classes](#classes)
     * [S: Princípio da Responsabilidade Única (SRP)](#princípio-da-responsabilidade-Única-srp)
     * [O: Princípio do Aberto/Fechado (OCP)](#princípio-do-abertofechado-ocp)
     * [L: Princício da Substituição de Liskov (LSP)](#princício-da-substituição-de-liskov-lsp)
     * [I: Princípio da Segregação de interface (ISP)](#princípio-da-segregação-de-interface-isp)
     * [D: Princípio da Injeção de dependências (DIP)](#princípio-da-injeção-de-dependências-dip)
     * [Use method chaining](#use-method-chaining)
     * [Prefira composição do que herança](#prefira-composição-do-que-herança)
  6. [Não repita você mesmo (DRY)](#não-repita-você-mesmo)
  7. [Traduções](#traduções)

## Introdução

O livro Software engineering principles, de Robert C. Martin
[*Clean Code*](https://www.amazon.com/Clean-Code-Handbook-Software-Craftsmanship/dp/0132350882),
adaptado para PHP. Isso não é um guia de estilo. É um guia para podução de software em PHP legível,
reusável, e refatorável.

Nem todos os princípios aqui contidos devem ser rigorosamente seguidos, e muito menos serão 
universalmente acordado. Estas são orientações e nada mais, mas são codificadas em muitos
anos de experiência coletiva pelos autores do *Clean Code*.

Inspirado em [clean-code-javascript](https://github.com/ryanmcdermott/clean-code-javascript)

## Variáveis

### Use variáveis pronunciaveis e com significado claro

**Ruim:**

```php
$ymdstr = $moment->format('y-m-d');
```

**Bom:**

```php
$currentDate = $moment->format('y-m-d');
```

**[⬆ voltar para o topo](#sumário)**

### Use o mesmo vocabulário para o mesmo tipo de variável

**Ruim:**

```php
getUserInfo();
getUserData();
getUserRecord();
getUserProfile();
```

**Bom:**

```php
getUser();
```

**[⬆ voltar para o topo](#sumário)**

### Use nomes pesquisáveis (parte 1)

Nós vamos ler mais código do que escrever. É importante que o código que nós escrevemos é
legível e pesquisável. Por *não* nomear variáveis que sejam significativas para o entendimento
do nosso programa, nós machucamos nossos leitores.
Faça variáveis com nomes pesquisáveis.

**Ruim:**

```php
// What the heck is 448 for?
$result = $serializer->serialize($data, 448);
```

**Bom:**

```php
$json = $serializer->serialize($data, JSON_UNESCAPED_SLASHES | JSON_PRETTY_PRINT | JSON_UNESCAPED_UNICODE);
```

### Use nomes pesquisáveis (parte 2)

**Ruim:**

```php
// What the heck is 4 for?
if ($user->access & 4) {
    // ...
}
```

**Bom:**

```php
class User
{
    const ACCESS_READ = 1;
    const ACCESS_CREATE = 2;
    const ACCESS_UPDATE = 4;
    const ACCESS_DELETE = 8;
}

if ($user->access & User::ACCESS_UPDATE) {
    // do edit ...
}
```

**[⬆ voltar para o topo](#sumário)**

### Use nomes explicativos

**Ruim:**

```php
$address = 'One Infinite Loop, Cupertino 95014';
$cityZipCodeRegex = '/^[^,\\]+[,\\\s]+(.+?)\s*(\d{5})?$/';
preg_match($cityZipCodeRegex, $address, $matches);

saveCityZipCode($matches[1], $matches[2]);
```

**Nada mal:**

É melhor, mas ainda depende do regex.

```php
$address = 'One Infinite Loop, Cupertino 95014';
$cityZipCodeRegex = '/^[^,\\]+[,\\\s]+(.+?)\s*(\d{5})?$/';
preg_match($cityZipCodeRegex, $address, $matches);

list(, $city, $zipCode) = $matches;
saveCityZipCode($city, $zipCode);
```

**Bom:**

Diminui a dependencia do regex nomeando os subpadrões.

```php
$address = 'One Infinite Loop, Cupertino 95014';
$cityZipCodeRegex = '/^[^,\\]+[,\\\s]+(?<city>.+?)\s*(?<zipCode>\d{5})?$/';
preg_match($cityZipCodeRegex, $address, $matches);

saveCityZipCode($matches['city'], $matches['zipCode']);
```

**[⬆ voltar para o topo](#sumário)**

### Evite mapa mental

Não forece o leitor do seu código traduzir o que as variáveis significam.
Explicito é melhor que implicito.

**Ruim:**

```php
$l = ['Austin', 'New York', 'San Francisco'];

for ($i = 0; $i < count($l); $i++) {
    $li = $l[$i];
    doStuff();
    doSomeOtherStuff();
    // ...
    // ...
    // ...
    // Wait, what is `$li` for again?
    dispatch($li);
}
```

**Bom:**

```php
$locations = ['Austin', 'New York', 'San Francisco'];

foreach ($locations as $location) {
    doStuff();
    doSomeOtherStuff();
    // ...
    // ...
    // ...
    dispatch($location);
}
```

**[⬆ voltar para o topo](#sumário)**

### Não coloque contexto desnecessário

Se o nome de sua classe/objeto diz alguma coisa, não repita no nome 
de suas variáveis.

**Ruim:**

```php
class Car
{
    public $carMake;
    public $carModel;
    public $carColor;

    //...
}
```

**Bom:**

```php
class Car
{
    public $make;
    public $model;
    public $color;

    //...
}
```

**[⬆ voltar para o topo](#sumário)**

### Use argumentos padrão ao invés de condicionais

**Nada mal:**
Isso não é nada mal porque `$breweryName` pode ser `NULL`.

```php
function createMicrobrewery($breweryName = 'Hipster Brew Co.')
{
    // ...
}
```

**Nada mal:**

Isso pode ser entendido melhor do que a versão anterior, mas é melhor ter o controle do valor da sua variável.

```php
function createMicrobrewery($name = null)
{
    $breweryName = $name ?: 'Hipster Brew Co.';
    // ...
}
```

**Bom:**

Se você tiver suporte para PHP 7+, você pode usar [type hinting](http://php.net/manual/en/functions.arguments.php#functions.arguments.type-declaration) e ter certeza que
`$breweryName` não será `NULL`.

```php
function createMicrobrewery(string $breweryName = 'Hipster Brew Co.')
{
    // ...
}
```

**[⬆ voltar para o topo](#sumário)**

## Funções

### Parâmetros de funções (2 ou menos)

Limitando a quantidade de parâmetros em uma função é importante porque é possivel testar
a função de forma mais fácil. Tendo mais de três conduz em uma explosão combinacional
onde você tem vários casos de teste de diferentes parâmetros separados.

Zero parâmetros é o caso ideal. Um ou dois parâmetros é ok, e três ou mais deve ser evitado.
Qualquer coisa mais deve ser consolidado. Frequentemente, se você tem mais de dois parâmetros
quer dizer que sua função está tentando fazer coisas demais. Em casos onde não está, 
você deve usar um objeto higher-level para suprir os parâmetros.

**Ruim:**

```php
function createMenu($title, $body, $buttonText, $cancellable)
{
    // ...
}
```

**Bom:**

```php
class MenuConfig
{
    public $title;
    public $body;
    public $buttonText;
    public $cancellable = false;
}

$config = new MenuConfig();
$config->title = 'Foo';
$config->body = 'Bar';
$config->buttonText = 'Baz';
$config->cancellable = true;

function createMenu(MenuConfig $config)
{
    // ...
}
```

**[⬆ voltar para o topo](#sumário)**

### Funções devem fazer apenas uma coisa

Esse é de longe a regra mais importante na engenharia de software. Quando funções fazem
mais do que uma coisa, elas são difíceis de utilizar, testar e racionalizar. Quando você
pode isolar uma função para apenas uma coisa, elas podem ser refatoradas facilmente e seu 
código será lido com muito mais clareza. Se você não pegar nada desse guia, além disso,
você vai está à frente de muitos desenvolvedores.

**Ruim:**
```php
function emailClients($clients)
{
    foreach ($clients as $client) {
        $clientRecord = $db->find($client);
        if ($clientRecord->isActive()) {
            email($client);
        }
    }
}
```

**Bom:**

```php
function emailClients($clients)
{
    $activeClients = activeClients($clients);
    array_walk($activeClients, 'email');
}

function activeClients($clients)
{
    return array_filter($clients, 'isClientActive');
}

function isClientActive($client)
{
    $clientRecord = $db->find($client);

    return $clientRecord->isActive();
}
```

**[⬆ voltar para o topo](#sumário)**

### Nome de função deve dizer o que ela faz

**Ruim:**

```php
class Email
{
    //...

    public function handle()
    {
        mail($this->to, $this->subject, $this->body);
    }
}

$message = new Email(...);
// What is this? A handle for the message? Are we writing to a file now?
$message->handle();
```

**Bom:**

```php
class Email 
{
    //...

    public function send()
    {
        mail($this->to, $this->subject, $this->body);
    }
}

$message = new Email(...);
// Clear and obvious
$message->send();
```

**[⬆ voltar para o topo](#sumário)**

### Funções devem ter apenas um nível de abstração

Quando você possui mais de um nível de abstração, sua função deve estar fazendo
coisas demais. Separar as funções leva a reusabilidade e testes de forma
mais fácil.

**Ruim:**

```php
function parseBetterJSAlternative($code)
{
    $regexes = [
        // ...
    ];

    $statements = explode(' ', $code);
    $tokens = [];
    foreach ($regexes as $regex) {
        foreach ($statements as $statement) {
            // ...
        }
    }

    $ast = [];
    foreach ($tokens as $token) {
        // lex...
    }

    foreach ($ast as $node) {
        // parse...
    }
}
```

**Bad too:**

Cuidamos de algumas funcionalidades, mas a função `parseBetterJSAlternative()`  ainda é muito complexa e não testavel.

```php
function tokenize($code)
{
    $regexes = [
        // ...
    ];

    $statements = explode(' ', $code);
    $tokens = [];
    foreach ($regexes as $regex) {
        foreach ($statements as $statement) {
            $tokens[] = /* ... */;
        }
    }

    return $tokens;
}

function lexer($tokens)
{
    $ast = [];
    foreach ($tokens as $token) {
        $ast[] = /* ... */;
    }

    return $ast;
}

function parseBetterJSAlternative($code)
{
    $tokens = tokenize($code);
    $ast = lexer($tokens);
    foreach ($ast as $node) {
        // parse...
    }
}
```

**Bom:**

A melhor solução é retirar as dependencias da função `parseBetterJSAlternative()`.

```php
class Tokenizer
{
    public function tokenize($code)
    {
        $regexes = [
            // ...
        ];

        $statements = explode(' ', $code);
        $tokens = [];
        foreach ($regexes as $regex) {
            foreach ($statements as $statement) {
                $tokens[] = /* ... */;
            }
        }

        return $tokens;
    }
}

class Lexer
{
    public function lexify($tokens)
    {
        $ast = [];
        foreach ($tokens as $token) {
            $ast[] = /* ... */;
        }

        return $ast;
    }
}

class BetterJSAlternative
{
    private $tokenizer;
    private $lexer;

    public function __construct(Tokenizer $tokenizer, Lexer $lexer)
    {
        $this->tokenizer = $tokenizer;
        $this->lexer = $lexer;
    }

    public function parse($code)
    {
        $tokens = $this->tokenizer->tokenize($code);
        $ast = $this->lexer->lexify($tokens);
        foreach ($ast as $node) {
            // parse...
        }
    }
}
```

**[⬆ voltar para o topo](#sumário)**

### Não use flags como parâmetros

Flags dizem para seu usuário que essa função faz mais de uma coisa. Funções devem
fazer apenas uma coisa. Divida suas funções se elas estão seguindo diferentes
diretórios baseado em um boleano.

**Ruim:**

```php
function createFile($name, $temp = false)
{
    if ($temp) {
        touch('./temp/'.$name);
    } else {
        touch($name);
    }
}
```

**Bom:**

```php
function createFile($name)
{
    touch($name);
}

function createTempFile($name)
{
    touch('./temp/'.$name);
}
```

**[⬆ voltar para o topo](#sumário)**

### Evite efeito colateral

Uma função produz um efeito colateral se ela faz mais do que pegar um valor e retornar
outro ou outros. Um efeito colateral pode ser, escrever em um arquivo, modificar
uma variável global, ou acidentalmente mandar todo seu dinheiro pra um estranho.

Agora, você pode ter efeitos colaterais em uma determinada situação. Como no exemplo
anterior, você pode precisar escrever em um arquivo. O que você quer fazer é centralizar
onde você está fazendo isso. Não tenha várias funções ou classes que escrevem em um
arquivo particular. Tenha um serviço que faça isso. Um e apenas um.

O ponto principal é evitar armadilhas comuns como compartilhar um estado entre objetos
sem qualquer estrutura, usando dados mutáveis que podem ser escritos por qualquer coisa,
e não centralizando onde os efeitos colaterais irão ocorrer. Se você pode fazer isso,
você ficará mais feliz do que a maioria dos outros programadores.

**Ruim:**

```php
// Global variable referenced by following function.
// If we had another function that used this name, now it'd be an array and it could break it.
$name = 'Ryan McDermott';

function splitIntoFirstAndLastName()
{
    global $name;

    $name = explode(' ', $name);
}

splitIntoFirstAndLastName();

var_dump($name); // ['Ryan', 'McDermott'];
```

**Bom:**

```php
function splitIntoFirstAndLastName($name)
{
    return explode(' ', $name);
}

$name = 'Ryan McDermott';
$newName = splitIntoFirstAndLastName($name);

var_dump($name); // 'Ryan McDermott';
var_dump($newName); // ['Ryan', 'McDermott'];
```

**[⬆ voltar para o topo](#sumário)**

### Não escreva funções globais

Criar globais é uma má prática em várias linguagens porque você pode colidir com outra 
biblioteca e não será claro para o usuário de sua API até que ele tenha uma exceção em
produção. Vamos pensar em um exemplo: se você quiser um array de configuração.
Vode poderia escrever uma função global como `config()`, mas pode colidir com outra biblioteca
que tentou fazer a mesma coisa.

**Ruim:**

```php
function config()
{
    return  [
        'foo' => 'bar',
    ]
}
```

**Bom:**

```php
class Configuration
{
    private $configuration = [];

    public function __construct(array $configuration)
    {
        $this->configuration = $configuration;
    }

    public function get($key)
    {
        return isset($this->configuration[$key]) ? $this->configuration[$key] : null;
    }
}
```

Carrega a configuração e crie uma instância da classe `Configuration`

```php
$configuration = new Configuration([
    'foo' => 'bar',
]);
```

Agora você deve usar a instância de `Configuration` na sua aplicação.

**[⬆ voltar para o topo](#sumário)**

### Não use o padrão Singleton

Singleton é um [anti-padrão](https://en.wikipedia.org/wiki/Singleton_pattern). Interpretado de 
Brian Button:
 1. Eles geralmente são usados como uma **instância global**, por que isso é tão ruim? Porque **você esconde as dependencias** da aplicação no seu código, em vez de expor eles através de interfaces. Fazendo alguma coisa global pra evitar que o [código cheire mal](https://en.wikipedia.org/wiki/Code_smell).
 2. Ele viola o [Princípio da Responsabilidade Única](#princípio-da-responsabilidade-Única-srp): pelo fato de **controlar sua prórpia crianção e ciclo de vida**.
 3. Eles inerentemente fazem com que o código seja muito [acoplado](https://en.wikipedia.org/wiki/Coupling_%28computer_programming%29). Isso faz com que eles resultem em falsos resultados em **testes** em muitos casos.
 4. Eles carregam o estado por todo o tempo de vida da aplicação. Um bateria de testes **para simular uma situação específica os testes precisam ser ordenados** deixando o propósito dos testes unitários de lado. Por quê? Porque cada teste unitário deve ser independente um do outro.

Também tem algumas ideias muitos boas do [Misko Hevery](http://misko.hevery.com/about/) falando sobre o [centro do problema](http://misko.hevery.com/2008/08/25/root-cause-of-singletons/).

**Ruim:**

```php
class DBConnection
{
    private static $instance;

    private function __construct($dsn)
    {
        // ...
    }

    public static function getInstance()
    {
        if (self::$instance === null) {
            self::$instance = new self();
        }

        return self::$instance;
    }

    // ...
}

$singleton = DBConnection::getInstance();
```

**Bom:**

```php
class DBConnection
{
    public function __construct(array $dsn)
    {
        // ...
    }

     // ...
}
```

Criar uma instância da classe `DBConnection` e configuar com [DSN](http://php.net/manual/en/pdo.construct.php#refsect1-pdo.construct-parameters).

```php
$connection = new DBConnection($dsn);
```

E agora você deve usar a instância de `DBConnection` na sua aplicação.

**[⬆ voltar para o topo](#sumário)**

### Encapsule condicionais

**Ruim:**

```php
if ($article->state === 'published') {
    // ...
}
```

**Bom:**

```php
if ($article->isPublished()) {
    // ...
}
```

**[⬆ voltar para o topo](#sumário)**

### Evite condicionais negativas

**Ruim:**

```php
function isDOMNodeNotPresent($node)
{
    // ...
}

if (!isDOMNodeNotPresent($node))
{
    // ...
}
```

**Bom:**

```php
function isDOMNodePresent($node)
{
    // ...
}

if (isDOMNodePresent($node)) {
    // ...
}
```

**[⬆ voltar para o topo](#sumário)**

### Evite condicionais

Essa parece ser uma tarefa impossível. A primeira coisa que as pessoas dizem
quando ouvem isso é "como eu poderia fazer qualquer coisa sem um `if`?" A
resposta é que você pode usar polimorfismo para chegar no mesmo resultado em
muitos casos. A segunda pergunta é, "Bom, isso é legal, mas por que eu faria 
isso?" A resposta é um outro conceito de código limpo que nós aprendemos:
Uma função deve fazer apenas uma coisa. Quando você tem classes e funções com
`if`, você está dizendo que sua função faz mais de uma coisa. Lembre-se,
só uma coisa.

**Ruim:**

```php
class Airplane
{
    // ...

    public function getCruisingAltitude()
    {
        switch ($this->type) {
            case '777':
                return $this->getMaxAltitude() - $this->getPassengerCount();
            case 'Air Force One':
                return $this->getMaxAltitude();
            case 'Cessna':
                return $this->getMaxAltitude() - $this->getFuelExpenditure();
        }
    }
}
```

**Bom:**

```php
interface Airplane
{
    // ...

    public function getCruisingAltitude();
}

class Boeing777 implements Airplane
{
    // ...

    public function getCruisingAltitude()
    {
        return $this->getMaxAltitude() - $this->getPassengerCount();
    }
}

class AirForceOne implements Airplane
{
    // ...

    public function getCruisingAltitude()
    {
        return $this->getMaxAltitude();
    }
}

class Cessna implements Airplane
{
    // ...

    public function getCruisingAltitude()
    {
        return $this->getMaxAltitude() - $this->getFuelExpenditure();
    }
}
```

**[⬆ voltar para o topo](#sumário)**

### Evite verificação de tipo (parte 1)

PHP não é fortemente tipado, isso quer dizer, que suas funções podem receber
qualquer tipo de argumento. Algumas vezes você é fisgado pela liberdade
e isso se tornar tentador fazer verificação de tipo nas suas funções.
Há várias maneiras de evitar fazer isso. A primeira é, APIs consistentes

**Ruim:**

```php
function travelToTexas($vehicle)
{
    if ($vehicle instanceof Bicycle) {
        $vehicle->peddleTo(new Location('texas'));
    } elseif ($vehicle instanceof Car) {
        $vehicle->driveTo(new Location('texas'));
    }
}
```

**Bom:**

```php
function travelToTexas(Traveler $vehicle)
{
    $vehicle->travelTo(new Location('texas'));
}
```

**[⬆ voltar para o topo](#sumário)**

### Evite verificação de tipo (parte 2)

Se você estiver trabalhando com tipos primitivos como strings, inteiros e arrays,
e você usa PHP 7+ e você não pode usar polimorfismo, mas você ainda sente a necessidade
de verificar os tipos, você deve considerar a [declaração de tipo](http://php.net/manual/en/functions.arguments.php#functions.arguments.type-declaration)
ou modo estrito. Isso permite que você use tipos estáticos por padrão na sintáxe da 
linguagem PHP. O problema com verificação de tipo manual é que isso requer muito mais
código escrito para ter os tipos validados, e você perde a legibilidade do código.
Mantenha seu PHP limpo, escreva bons testes, e tenha boas revisões. Caso contrário, 
faça tudo como o modo estrito do PHP.

**Ruim:**

```php
function combine($val1, $val2)
{
    if (!is_numeric($val1) || !is_numeric($val2)) {
        throw new \Exception('Must be of type Number');
    }

    return $val1 + $val2;
}
```

**Bom:**

```php
function combine(int $val1, int $val2)
{
    return $val1 + $val2;
}
```

**[⬆ voltar para o topo](#sumário)**

### Remova código morto

Código morto é tão ruim quanto duplicar o código. Não há motivos para manter
ele no seu projeto. Se ele não está sendo chamado, livre-se dele! Ele ainda 
está seguro no histórico de versão quando você precisar dele.

**Ruim:**

```php
function oldRequestModule($url)
{
    // ...
}

function newRequestModule($url)
{
    // ...
}

$request = newRequestModule($requestUrl);
inventoryTracker('apples', $request, 'www.inventory-awesome.io');
```

**Bom:**

```php
function requestModule($url)
{
    // ...
}

$request = requestModule($requestUrl);
inventoryTracker('apples', $request, 'www.inventory-awesome.io');
```

**[⬆ voltar para o topo](#sumário)**


## Objetos e estrutura de dados

### Use getters e setters

In PHP you can set `public`, `protected` and `private` keywords for methods. 
Using it, you can control properties modification on an object. 

* When you want to do more beyond getting an object property, you don't have
to look up and change every accessor in your codebase.
* Makes adding validation simple when doing a `set`.
* Encapsulates the internal representation.
* Easy to add logging and error handling when getting and setting.
* Inheriting this class, you can override default functionality.
* You can lazy load your object's properties, let's say getting it from a
server.

Additionally, this is part of Princípio do Aberto/Fechado, from object-oriented 
design principles.

**Ruim:**

```php
class BankAccount
{
    public $balance = 1000;
}

$bankAccount = new BankAccount();

// Buy shoes...
$bankAccount->balance -= 100;
```

**Bom:**

```php
class BankAccount
{
    private $balance;

    public function __construct($balance = 1000)
    {
      $this->balance = $balance;
    }

    public function withdrawBalance($amount)
    {
        if ($amount > $this->balance) {
            throw new \Exception('Amount greater than available balance.');
        }

        $this->balance -= $amount;
    }

    public function depositBalance($amount)
    {
        $this->balance += $amount;
    }

    public function getBalance()
    {
        return $this->balance;
    }
}

$bankAccount = new BankAccount();

// Buy shoes...
$bankAccount->withdrawBalance($shoesPrice);

// Get balance
$balance = $bankAccount->getBalance();
```

**[⬆ voltar para o topo](#sumário)**

### Faça objetos terem membros private/protected

**Ruim:**

```php
class Employee
{
    public $name;

    public function __construct($name)
    {
        $this->name = $name;
    }
}

$employee = new Employee('John Doe');
echo 'Employee name: '.$employee->name; // Employee name: John Doe
```

**Bom:**

```php
class Employee
{
    private $name;

    public function __construct($name)
    {
        $this->name = $name;
    }

    public function getName()
    {
        return $this->name;
    }
}

$employee = new Employee('John Doe');
echo 'Employee name: '.$employee->getName(); // Employee name: John Doe
```

**[⬆ voltar para o topo](#sumário)**

## Classes

### Princípio da Responsabilidade Única (SRP)

As stated in Clean Code, "There should never be more than one reason for a class
to change". It's tempting to jam-pack a class with a lot of functionality, like
when you can only take one suitcase on your flight. The issue with this is
that your class won't be conceptually cohesive and it will give it many reasons
to change. Minimizing the amount of times you need to change a class is important.
It's important because if too much functionality is in one class and you modify a piece of it,
it can be difficult to understand how that will affect other dependent modules in
your codebase.

**Ruim:**

```php
class UserSettings
{
    private $user;

    public function __construct($user)
    {
        $this->user = $user;
    }

    public function changeSettings($settings)
    {
        if ($this->verifyCredentials()) {
            // ...
        }
    }

    private function verifyCredentials()
    {
        // ...
    }
}
```

**Bom:**

```php
class UserAuth 
{
    private $user;

    public function __construct($user)
    {
        $this->user = $user;
    }
    
    public function verifyCredentials()
    {
        // ...
    }
}

class UserSettings 
{
    private $user;
    private $auth;

    public function __construct($user) 
    {
        $this->user = $user;
        $this->auth = new UserAuth($user);
    }

    public function changeSettings($settings)
    {
        if ($this->auth->verifyCredentials()) {
            // ...
        }
    }
}
```

**[⬆ voltar para o topo](#sumário)**

### Princípio do Aberto/Fechado (OCP)

As stated by Bertrand Meyer, "software entities (classes, modules, functions,
etc.) should be open for extension, but closed for modification." What does that
mean though? This principle basically states that you should allow users to
add new functionalities without changing existing code.

**Ruim:**

```php
abstract class Adapter
{
    protected $name;

    public function getName()
    {
        return $this->name;
    }
}

class AjaxAdapter extends Adapter
{
    public function __construct()
    {
        parent::__construct();

        $this->name = 'ajaxAdapter';
    }
}

class NodeAdapter extends Adapter
{
    public function __construct()
    {
        parent::__construct();

        $this->name = 'nodeAdapter';
    }
}

class HttpRequester
{
    private $adapter;

    public function __construct($adapter)
    {
        $this->adapter = $adapter;
    }

    public function fetch($url)
    {
        $adapterName = $this->adapter->getName();

        if ($adapterName === 'ajaxAdapter') {
            return $this->makeAjaxCall($url);
        } elseif ($adapterName === 'httpNodeAdapter') {
            return $this->makeHttpCall($url);
        }
    }

    private function makeAjaxCall($url)
    {
        // request and return promise
    }

    private function makeHttpCall($url)
    {
        // request and return promise
    }
}
```

**Bom:**

```php
interface Adapter
{
    public function request($url);
}

class AjaxAdapter implements Adapter
{
    public function request($url)
    {
        // request and return promise
    }
}

class NodeAdapter implements Adapter
{
    public function request($url)
    {
        // request and return promise
    }
}

class HttpRequester
{
    private $adapter;

    public function __construct(Adapter $adapter)
    {
        $this->adapter = $adapter;
    }

    public function fetch($url)
    {
        return $this->adapter->request($url);
    }
}
```

**[⬆ voltar para o topo](#sumário)**

### Princício da Substituição de Liskov (LSP)

This is a scary term for a very simple concept. It's formally defined as "If S
is a subtype of T, then objects of type T may be replaced with objects of type S
(i.e., objects of type S may substitute objects of type T) without altering any
of the desirable properties of that program (correctness, task performed,
etc.)." That's an even scarier definition.

The best explanation for this is if you have a parent class and a child class,
then the base class and child class can be used interchangeably without getting
incorrect results. This might still be confusing, so let's take a look at the
classic Square-Rectangle example. Mathematically, a square is a rectangle, but
if you model it using the "is-a" relationship via inheritance, you quickly
get into trouble.

**Ruim:**

```php
class Rectangle
{
    protected $width = 0;
    protected $height = 0;

    public function render($area)
    {
        // ...
    }

    public function setWidth($width)
    {
        $this->width = $width;
    }

    public function setHeight($height)
    {
        $this->height = $height;
    }

    public function getArea()
    {
        return $this->width * $this->height;
    }
}

class Square extends Rectangle
{
    public function setWidth($width)
    {
        $this->width = $this->height = $width;
    }

    public function setHeight(height)
    {
        $this->width = $this->height = $height;
    }
}

function renderLargeRectangles($rectangles)
{
    foreach ($rectangles as $rectangle) {
        $rectangle->setWidth(4);
        $rectangle->setHeight(5);
        $area = $rectangle->getArea(); // BAD: Will return 25 for Square. Should be 20.
        $rectangle->render($area);
    }
}

$rectangles = [new Rectangle(), new Rectangle(), new Square()];
renderLargeRectangles($rectangles);
```

**Bom:**

```php
abstract class Shape
{
    protected $width = 0;
    protected $height = 0;

    abstract public function getArea();

    public function render($area)
    {
        // ...
    }
}

class Rectangle extends Shape
{
    public function setWidth($width)
    {
        $this->width = $width;
    }

    public function setHeight($height)
    {
        $this->height = $height;
    }

    public function getArea()
    {
        return $this->width * $this->height;
    }
}

class Square extends Shape
{
    private $length = 0;

    public function setLength($length)
    {
        $this->length = $length;
    }

    public function getArea()
    {
        return pow($this->length, 2);
    }
}

function renderLargeRectangles($rectangles)
{
    foreach ($rectangles as $rectangle) {
        if ($rectangle instanceof Square) {
            $rectangle->setLength(5);
        } elseif ($rectangle instanceof Rectangle) {
            $rectangle->setWidth(4);
            $rectangle->setHeight(5);
        }

        $area = $rectangle->getArea(); 
        $rectangle->render($area);
    }
}

$shapes = [new Rectangle(), new Rectangle(), new Square()];
renderLargeRectangles($shapes);
```

**[⬆ voltar para o topo](#sumário)**

### Princípio da Segregação de interface (ISP)

ISP states that "Clients should not be forced to depend upon interfaces that
they do not use." 

A good example to look at that demonstrates this principle is for
classes that require large settings objects. Not requiring clients to setup
huge amounts of options is beneficial, because most of the time they won't need
all of the settings. Making them optional helps prevent having a "fat interface".

**Ruim:**

```php
interface Employee
{
    public function work();

    public function eat();
}

class Human implements Employee
{
    public function work()
    {
        // ....working
    }

    public function eat()
    {
        // ...... eating in lunch break
    }
}

class Robot implements Employee
{
    public function work()
    {
        //.... working much more
    }

    public function eat()
    {
        //.... robot can't eat, but it must implement this method
    }
}
```

**Bom:**

Not every worker is an employee, but every employee is an worker.

```php
interface Workable
{
    public function work();
}

interface Feedable
{
    public function eat();
}

interface Employee extends Feedable, Workable
{
}

class Human implements Employee
{
    public function work()
    {
        // ....working
    }

    public function eat()
    {
        //.... eating in lunch break
    }
}

// robot can only work
class Robot implements Workable
{
    public function work()
    {
        // ....working
    }
}
```

**[⬆ voltar para o topo](#sumário)**

### Princípio da Injeção de dependências (DIP)

This principle states two essential things:
1. High-level modules should not depend on low-level modules. Both should
depend on abstractions.
2. Abstractions should not depend upon details. Details should depend on
abstractions.

This can be hard to understand at first, but if you've worked with PHP frameworks (like Symfony), you've seen an implementation of this principle in the form of Dependency
Injection (DI). While they are not identical concepts, DIP keeps high-level
modules from knowing the details of its low-level modules and setting them up.
It can accomplish this through DI. A huge benefit of this is that it reduces
the coupling between modules. Coupling is a very bad development pattern because
it makes your code hard to refactor.

**Ruim:**

```php
class Employee
{
    public function work()
    {
        // ....working
    }
}

class Robot extends Employee
{
    public function work()
    {
        //.... working much more
    }
}

class Manager
{
    private $employee;

    public function __construct(Employee $employee)
    {
        $this->employee = $employee;
    }

    public function manage()
    {
        $this->employee->work();
    }
}
```

**Bom:**

```php
interface Employee
{
    public function work();
}

class Human implements Employee
{
    public function work()
    {
        // ....working
    }
}

class Robot implements Employee
{
    public function work()
    {
        //.... working much more
    }
}

class Manager
{
    private $employee;

    public function __construct(Employee $employee)
    {
        $this->employee = $employee;
    }

    public function manage()
    {
        $this->employee->work();
    }
}
```

**[⬆ voltar para o topo](#sumário)**

### Use method chaining

This pattern is very useful and commonly used in many libraries such
as PHPUnit and Doctrine. It allows your code to be expressive, and less verbose.
For that reason, use method chaining and take a look at how clean your code
will be. In your class functions, simply use `return $this` at the end of every `set` function,
and you can chain further class methods onto it.

**Ruim:**

```php
class Car 
{
    private $make = 'Honda';
    private $model = 'Accord';
    private $color = 'white';

    public function setMake($make)
    {
        $this->make = $make;
    }

    public function setModel($model)
    {
        $this->model = $model;
    }

    public function setColor($color)
    {
        $this->color = $color;
    }

    public function dump()
    {
        var_dump($this->make, $this->model, $this->color);
    }
}

$car = new Car();
$car->setColor('pink');
$car->setMake('Ford');
$car->setModel('F-150');
$car->dump();
```

**Bom:**

```php
class Car 
{
    private $make = 'Honda';
    private $model = 'Accord';
    private $color = 'white';

    public function setMake($make)
    {
        $this->make = $make;
        
        // NOTE: Returning this for chaining
        return $this;
    }

    public function setModel($model)
    {
        $this->model = $model;

        // NOTE: Returning this for chaining
        return $this;
    }

    public function setColor($color)
    {
        $this->color = $color;

        // NOTE: Returning this for chaining
        return $this;
    }

    public function dump()
    {
        var_dump($this->make, $this->model, $this->color);
    }
}

$car = (new Car())
  ->setColor('pink')
  ->setMake('Ford')
  ->setModel('F-150')
  ->dump();
```

**[⬆ voltar para o topo](#sumário)**

### Prefira composição do que herança

As stated famously in [*Design Patterns*](https://en.wikipedia.org/wiki/Design_Patterns) by the Gang of Four,
you should Prefira composição do que herança where you can. There are lots of
good reasons to use inheritance and lots of good reasons to use composition.
The main point for this maxim is that if your mind instinctively goes for
inheritance, try to think if composition could model your problem better. In some
cases it can.

You might be wondering then, "when should I use inheritance?" It
depends on your problem at hand, but this is a decent list of when inheritance
makes more sense than composition:

1. Your inheritance represents an "is-a" relationship and not a "has-a"
relationship (Human->Animal vs. User->UserDetails).
2. You can reuse code from the base classes (Humans can move like all animals).
3. You want to make global changes to derived classes by changing a base class.
(Change the caloric expenditure of all animals when they move).

**Ruim:**

```php
class Employee 
{
    private $name;
    private $email;

    public function __construct($name, $email)
    {
        $this->name = $name;
        $this->email = $email;
    }

    // ...
}

// Bad because Employees "have" tax data. 
// EmployeeTaxData is not a type of Employee

class EmployeeTaxData extends Employee 
{
    private $ssn;
    private $salary;
    
    public function __construct($name, $email, $ssn, $salary)
    {
        parent::__construct($name, $email);

        $this->ssn = $ssn;
        $this->salary = $salary;
    }

    // ...
}
```

**Bom:**

```php
class EmployeeTaxData 
{
    private $ssn;
    private $salary;

    public function __construct($ssn, $salary)
    {
        $this->ssn = $ssn;
        $this->salary = $salary;
    }

    // ...
}

class Employee 
{
    private $name;
    private $email;
    private $taxData;

    public function __construct($name, $email)
    {
        $this->name = $name;
        $this->email = $email;
    }

    public function setTaxData($ssn, $salary)
    {
        $this->taxData = new EmployeeTaxData($ssn, $salary);
    }

    // ...
}
```

**[⬆ voltar para o topo](#sumário)**

## Não repita você mesmo (DRY)

Try to observe the [DRY](https://en.wikipedia.org/wiki/Don%27t_repeat_yourself) principle.

Do your absolute best to avoid duplicate code. Duplicate code is bad because 
it means that there's more than one place to alter something if you need to 
change some logic.

Imagine if you run a restaurant and you keep track of your inventory: all your 
tomatoes, onions, garlic, spices, etc. If you have multiple lists that
you keep this on, then all have to be updated when you serve a dish with
tomatoes in them. If you only have one list, there's only one place to update!

Oftentimes you have duplicate code because you have two or more slightly
different things, that share a lot in common, but their differences force you
to have two or more separate functions that do much of the same things. Removing 
duplicate code means creating an abstraction that can handle this set of different 
things with just one function/module/class.

Getting the abstraction right is critical, that's why you should follow the
SOLID principles laid out in the [Classes](#classes) section. Bad abstractions can be
worse than duplicate code, so be careful! Having said this, if you can make
a good abstraction, do it! Don't repeat yourself, otherwise you'll find yourself 
updating multiple places anytime you want to change one thing.

**Ruim:**

```php
function showDeveloperList($developers)
{
    foreach ($developers as $developer) {
        $expectedSalary = $developer->calculateExpectedSalary();
        $experience = $developer->getExperience();
        $githubLink = $developer->getGithubLink();
        $data = [
            $expectedSalary,
            $experience,
            $githubLink
        ];

        render($data);
    }
}

function showManagerList($managers)
{
    foreach ($managers as $manager) {
        $expectedSalary = $manager->calculateExpectedSalary();
        $experience = $manager->getExperience();
        $githubLink = $manager->getGithubLink();
        $data = [
            $expectedSalary,
            $experience,
            $githubLink
        ];

        render($data);
    }
}
```

**Bom:**

```php
function showList($employees)
{
    foreach ($employees as $employee) {
        $expectedSalary = $employee->calculateExpectedSalary();
        $experience = $employee->getExperience();
        $githubLink = $employee->getGithubLink();
        $data = [
            $expectedSalary,
            $experience,
            $githubLink
        ];

        render($data);
    }
}
```

**Very good:**

It is better to use a compact version of the code.

```php
function showList($employees)
{
    foreach ($employees as $employee) {
        render([
            $employee->calculateExpectedSalary(),
            $employee->getExperience(),
            $employee->getGithubLink()
        ]);
    }
}
```

**[⬆ voltar para o topo](#sumário)**

## Traduções

This is also available in other languages:

 * :cn: **Chinese:**
   * [php-cpm/clean-code-php](https://github.com/php-cpm/clean-code-php)
 * :ru: **Russian:**
   * [peter-gribanov/clean-code-php](https://github.com/peter-gribanov/clean-code-php)
 * :es: **Spanish:**
   * [fikoborquez/clean-code-php](https://github.com/fikoborquez/clean-code-php)
 * :thailand: **Thai:**
   * [panuwizzle/clean-code-php](https://github.com/panuwizzle/clean-code-php)
 * :fr: **French:**
   * [errorname/clean-code-php](https://github.com/errorname/clean-code-php)
 * :vietnam: **Vietnamese**
   * [viethuongdev/clean-code-php](https://github.com/viethuongdev/clean-code-php)
 * :kr: **Korean:**
   * [yujineeee/clean-code-php](https://github.com/yujineeee/clean-code-php)
 * :tr: **Turkish:**
   * [anilozmen/clean-code-php](https://github.com/anilozmen/clean-code-php)   

**[⬆ voltar para o topo](#sumário)**
