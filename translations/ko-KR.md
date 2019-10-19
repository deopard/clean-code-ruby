# clean-code-ruby

Ruby를 위한 클린 코드.

[clean-code-javascript](https://github.com/ryanmcdermott/clean-code-javascript)에 영감을 받아 만들어짐.

*Note: The examples are largely ported over from JavaScript so they may not be idiomatic. Feel free to point out any non-idiomatic Ruby code by submitting an issue and I'll correct it right away. Also, pull requests are always welcome!*

## 목차
  1. [소개](#소개)
  2. [변수](#변수)
  3. [메소드](#메소드)
  4. [Objects and Data Structures](#objects-and-data-structures)
  5. [Classes](#classes)
  6. [SOLID](#solid)
  7. [Testing](#testing)
  9. [Error Handling](#error-handling)
  10. [Formatting](#formatting)
  11. [Comments](#comments)
  12. [Translations](#translations)

## 소개
![Humorous image of software quality estimation as a count of how many expletives
you shout when reading code](http://www.osnews.com/images/comics/wtfm.jpg)

Software engineering principles, from Robert C. Martin's book
[*Clean Code*](https://www.amazon.com/Clean-Code-Handbook-Software-Craftsmanship/dp/0132350882),
adapted for Ruby. This is not a style guide. It's a guide to producing
[readable, reusable, and refactorable](https://github.com/ryanmcdermott/3rs-of-software-architecture) software in Ruby.

Not every principle herein has to be strictly followed, and even fewer will be
universally agreed upon. These are guidelines and nothing more, but they are
ones codified over many years of collective experience by the authors of
*Clean Code*.

Our craft of software engineering is just a bit over 50 years old, and we are
still learning a lot. When software architecture is as old as architecture
itself, maybe then we will have harder rules to follow. For now, let these
guidelines serve as a touchstone by which to assess the quality of the
Ruby code that you and your team produce.

One more thing: knowing these won't immediately make you a better software
developer, and working with them for many years doesn't mean you won't make
mistakes. Every piece of code starts as a first draft, like wet clay getting
shaped into its final form. Finally, we chisel away the imperfections when
we review it with our peers. Don't beat yourself up for first drafts that need
improvement. Beat up the code instead!

## **변수**
### 의미있고 읽을 수 있는 변수명을 사용해라

**나쁨:**
```ruby
yyyymmdstr = Time.now.strftime('%Y/%m/%d')
```

**좋음:**
```ruby
current_date = Time.now.strftime('%Y/%m/%d')
```
**[⬆ 맨 위로 이동](#목차)**

### 같은 종류의 변수에 대해서는 같은 어휘를 사용해라

해당 개념에 맞는 한 가지 단어를 고르고 해당 단어만 사용해라.

**나쁨:**
```ruby
user_info
user_data
user_record

starts_at
start_at
start_time
```

**좋음:**
```ruby
user

starts_at
```
**[⬆ 맨 위로 이동](#목차)**

### 검색 가능한 이름을 사용하고 상수를 사용해라
코드는 쓰여지는 것 보다 읽혀질 일이 더 많다. 가독성이 좋고 검색 가능한 코드를 작성하는 것은 매우 중요하다. 프로그램을 이해하는데 의미 없는 변수명을 사용하게 되면 독자를 방해하게 될 것이다.
검색 가능한 이름을 지어라.

그리고 "매직 넘버"를 이용하거나 값을 하드코딩하여 넣는 대신 상수를 만들어라.

**나쁨:**
```ruby
# 도대체 86400는 왜있는거야?
status = Timeout::timeout(86_400) do
  # ...
end
```

**좋음:**
```ruby
# 대문자로 쓰여진 전역 상수로 선언해라.
SECONDS_IN_A_DAY = 86_400

status = Timeout::timeout(SECONDS_IN_A_DAY) do
  # ...
end
```
**[⬆ 맨 위로 이동](#목차)**

### 설명하기 위한 변수를 사용해라
**나쁨:**
```ruby
address = 'One Infinite Loop, Cupertino 95014'
city_zip_code_regex = /^[^,\\]+[,\\\s]+(.+?)\s*(\d{5})?$/
save_city_zip_code(city_zip_code_regex.match(address)[1], city_zip_code_regex.match(address)[2])
```

**좋음:**
```ruby
address = 'One Infinite Loop, Cupertino 95014'
city_zip_code_regex = /^[^,\\]+[,\\\s]+(.+?)\s*(\d{5})?$/
_, city, zip_code = city_zip_code_regex.match(address).to_a
save_city_zip_code(city, zip_code)
```
**[⬆ 맨 위로 이동](#목차)**

### 멘탈 매핑을 피해라
명시적인 것이 암묵적인 것 보다 낫다.

**나쁨:**
```ruby
locations = ['Austin', 'New York', 'San Francisco']
locations.each do |l|
  do_stuff
  do_some_other_stuff
  # ...
  # ...
  # ...
  # 잠깐, `l`이 뭐였지?
  dispatch(l)
end
```

**좋음:**
```ruby
locations = ['Austin', 'New York', 'San Francisco']
locations.each do |location|
  do_stuff
  do_some_other_stuff
  # ...
  # ...
  # ...
  dispatch(location)
end
```
**[⬆ 맨 위로 이동](#목차)**

### 불필요한 문맥을 추가하지 마라
만약에 너의 클래스/객체가 무엇을 의미한다면 그 의미를 변수명에 반복해서 사용하지 마라.

**나쁨:**
```ruby
car = {
  car_make: 'Honda',
  car_model: 'Accord',
  car_color: 'Blue'
}

def paint_car(car)
  car[:car_color] = 'Red'
end
```

**좋음:**
```ruby
car = {
  make: 'Honda',
  model: 'Accord',
  color: 'Blue'
}

def paint_car(car)
  car[:color] = 'Red'
end
```
**[⬆ 맨 위로 이동](#목차)**

### short circuiting이나 조건문 대신 기본 값 인자를 사용해라
기본 값 인수는 보통 short circuiting보다 깔끔하다. 기본 값 인수를 사용하면 너의
메소드는 정의되지 않은 인수에 대해 제공된 기본 값만 사용하는 것을 주의해라.
`false`나 `nil`과 같은 "falsy"한 값들은 기본 값으로 대체되지 않는다.

**나쁨:**
```ruby
def create_micro_brewery(name)
  brewery_name = name || 'Hipster Brew Co.'
  # ...
end
```

**좋음:**
```ruby
def create_micro_brewery(brewery_name = 'Hipster Brew Co.')
  # ...
end
```
**[⬆ 맨 위로 이동](#목차)**

## **메소드**
### 메소드 인수(이상적으로 2개 이하)
메소드 인수의 수를 제한하는 것은 메소드의 테스트를 더 쉽게 해주기 때문에 매우 중요하다.
3개 이상을 갖게 되면 인자의 조합에 따른 경우의 수가 많아져 테스트해야 할 수가 수 없이 많아진다.

하나 혹은 두 개의 인자가 이상적이고 세 개는 되도록이면 피해야 한다. 4개 이상은 정리되야 한다.
보통 3개 이상의 인자가 필요한 경우는 메소드에서 너무 많은 일을 하려고 할 때다. 그렇지 않은
경우에는 상위 레벨의 객체를 인자를 사용하는 것으로 해결할 수 있다. 또는 인스턴스 변수를 이용하여
메소드에 데이터를 넘겨줄 수도 있다.

Ruby에서는 여러 클래스를 만들지 않도고 객체를 쉽게 만들 수 있으므로 여러개의 인수가 필요하다면 
객체를 사용할 수 있다. Ruby에서 더 우세한 패턴은 해시 인자를 사용하는 것이다.

메소드가 기대하는 속성을 더 명확하게 하기 위하여 키워드 인수 문법(Ruby 2.1부터 사용 가능)을 사용할 수 도 있다.
이는 몇 가지 이점을 가져다 준다.

1. 어떤 사람이 메소드 시그니처를 보면 바로 어떤 속성이 이용되는지 이해할 수 있다.
2. 만약에 필수 키워드 인수가 없는 경우에 Ruby에서는 우리게에 어떤 인수가 필수인지 알려주는 `ArgumentError`를 발생시킨다. 

**나쁨:**
```ruby
def create_menu(title, body)
  # ...
end
```

**좋음:**
```ruby
def create_menu(title:, body:)
  # ...
end

create_menu(title: 'Foo', body: 'Bar')
```
**[⬆ 맨 위로 이동](#목차)**


### 메소드는 한 가지 일만 해야 한다.
이는 소프트웨어 엔지니어링에 있어 가장 중요한 규칙이다. 메소드가 한 가지 이상의 일을
하게 되면 작성, 테스트 및 추론하기가 어려워진다. 메소드를 하나의 액션으로 분리한다면 
더 쉽게 리팩토링 할 수 있으며 코드는 훨씬 깔끔해질 것이다. 이것만 지켜도 많은 
개발자들보다 앞서나갈 수 있다.

**나쁨:**
```ruby
def email_clients(clients)
  clients.each do |client|
    client_record = database.lookup(client)
    email(client) if client_record.active?
  end
end

email_clients(clients)
```

**좋음:**
```ruby
def email_clients(clients)
  clients.each { |client| email(client) }
end

def active_clients(clients)
  clients.select { |client| active_client?(client) }
end

def active_client?(client)
  client_record = database.lookup(client)
  client_record.active?
end

email_clients(active_clients(clients))
```
**[⬆ 맨 위로 이동](#목차)**

### 메소드 이름은 메소드가 어떤 일을 하는지 나타내야 한다
잘못 지어진 메소드 이름은 코드 리뷰어의 인지 부하를 일으키며 코드 리뷰어가 이해를 잘못하게 할 수 있다.
메소드의 이름을 지을 때는 정확한 의도를 표현할 수 있도록 노력해라.

**나쁨:**
```ruby
def add_to_date(date, month)
  # ...
end

date = DateTime.now

# 무엇이 더해졌는지 메소드 이름만 봐서는 알기 어렵다.
add_to_date(date, 1)
```

**좋음:**
```ruby
def add_month_to_date(date, month)
  # ...
end

date = DateTime.now
add_month_to_date(date, 1)
```
**[⬆ 맨 위로 이동](#목차)**

### 메소드는 한 단계의 추상화만 되야 한다
보통 두 단계 이상의 추상화를 하는 경우엔 메소드가 너무 많은 일을 하고 있는 것이다.
메소드를 나누는 것은 재사용성을 높여주고 테스트하기 쉽게 만들어준다. 
더불어 메소드는 추상화 단계를 줄여야 한다: 추상적인 메소드는 자기보다 덜 추상적인 메소드를 호출하는 식으로 반복되야 한다.

**나쁨:**
```ruby
def interpret(code)
  regexes = [
    # ...
  ]

  statements = code.split(' ')
  tokens = []
  regexes.each do |regex|
    statements.each do |statement|
      # ...
    end
  end

  ast = []
  tokens.each do |token|
    # lex...
  end

  result = []
  ast.each do |node|
    # result.push(...)
  end

  result
end
```

**좋음:**
```ruby
def interpret(code)
  tokens = tokenize(code)
  ast = lex(tokens)
  parse(ast)
end

def tokenize(code)
  regexes = [
    # ...
  ]

  statements = code.split(' ')
  tokens = []
  regexes.each do |regex|
    statements.each do |statement|
      # tokens.push(...)
    end
  end

  tokens
end

def lex(tokens)
  ast = []
  tokens.each do |token|
    # ast.push(...)
  end

  ast
end

def parse(ast)
  result = []
  ast.each do |node|
    # result.push(...)
  end

  result
end
```
**[⬆ 맨 위로 이동](#목차)**

### 중복되는 코드를 없애라
중복되는 코드를 없애기 위해 무조건 최선을 다해라. 중복되는 코드는 로직을 변경하고 싶을
때 두 군데 이상을 수정해야 한다는 뜻이기 때문에 나쁘다.

너가 음식점을 운영하고 재고를 관리하고 있다고 상상해보자: 토마토, 양파, 마늘, 향신료 등.
만약에 너가 재고를 관리하는 목록을 두 개 이상 갖고 있다면 토마토를 포함한 음식을 하나
내놓을 때마다 모든 목록을 업데이트해야 한다. 만약에 한 개의 목록만 갖고 있다면 그 하나만
수정하면 된다!

두 개 이상의 것들이 대부분 비슷하지만 아주 약간 다르며 그 차이점이 두 개 이상의 분리된
메소드를 이용할 수 밖에 없을 때 중복되는 코드가 자주 생기게 된다.
이런 차이점을 관리할 수 있도록 추상화된 하나의 메소드/모듈/클래스를 만들어 중복된 코드를 
없앨 수 있다.

옳바른 추상화를 하는 것은 매우 중요하기 때문에 *클래스* 영역에서 다루고 있는 SOLID 원칙을 
따라야 한다. 잘못된 추상화는 중복된 코드보다도 더 나쁠 수 있기 때문에 조심해야 한다!
반대로 좋은 추상화를 할 수 있다면 꼭 해라! 한 가지 변경을 하고 싶어도 여러 곳을 변경하기 
싫다면 반복해서 코드를 작성하지 마라.

**나쁨:**
```ruby
def show_developer_list(developers)
  developers.each do |developer|
    data = {
      expected_salary: developer.expected_salary,
      experience: developer.experience,
      github_link: developer.github_link
    }

    render(data)
  end
end

def show_manager_list(managers)
  managers.each do |manager|
    data = {
      expected_salary: manager.expected_salary,
      experience: manager.experience,
      portfolio: manager.mba_projects
    }

    render(data)
  end
end
```

**좋음:**
```ruby
def show_employee_list(employees)
  employees.each do |employee|
    data = {
      expected_salary: employee.expected_salary,
      experience: employee.experience
    }

    case employee.type
    when 'manager'
      data[:portfolio] = employee.mba_projects
    when 'developer'
      data[:github_link] = employee.github_link
    end

    render(data)
  end
end
```
**[⬆ 맨 위로 이동](#목차)**

### 플래그를 메소드 인수로 사용하지 마라
플래그는 이 메소드가 한 가지보다 많은 일을 한다는 것을 말해준다. 메소드는 한 가지 일만 해야 한다. 
부울 변수에 따라 다른 코드를 실행한다면 메소드를 나눠라.

**나쁨:**
```ruby
def create_file(name, temp)
  if temp
    fs.create("./temp/#{name}")
  else
    fs.create(name)
  end
end
```

**좋음:**
```ruby
def create_file(name)
  fs.create(name)
end

def create_temp_file(name)
  create_file("./temp/#{name}")
end
```
**[⬆ 맨 위로 이동](#목차)**

### Avoid Side Effects (part 1)
A method produces side effects if it does anything more than take values and/or
return values. A side effect could be writing to a file,
modifying some global variable, or accidentally wiring all your money to a
stranger.

Now, you do need to have side effects in a program on occasion. Like the previous
example, you might need to write to a file. What you want to do is to
centralize where you are doing this. Don't have several methods and classes
that write to a particular file. Have one service that does it. One and only one.

The main point is to avoid common pitfalls like sharing state between objects
without any structure, using mutable data types that can be written to by anything,
and not centralizing where your side effects occur. If you can do this, you will
be happier than the vast majority of other programmers.

**나쁨:**
```ruby
# Global variable referenced by following method.
# If we had another method that used this name, now it'd be an array and it could break it.
name = 'Ryan McDermott'

def split_into_first_and_last_name
  name = $name.split(' ')
end

split_into_first_and_last_name()

puts name # ['Ryan', 'McDermott']
```

**좋음:**
```ruby
def split_into_first_and_last_name(name)
  name.split(' ')
end

name = 'Ryan McDermott'
first_and_last_name = split_into_first_and_last_name(name)

puts name # 'Ryan McDermott'
puts first_and_last_name # ['Ryan', 'McDermott']
```
**[⬆ 맨 위로 이동](#목차)**

### Avoid Side Effects (part 2)
In Ruby, everything is an object and everything is passed by value, but these values are references to objects. In the case of objects and arrays, if your method makes a change
in a shopping cart array, for example, by adding an item to purchase,
then any other method that uses that `cart` array will be affected by this
addition. That may be great, however it can be bad too. Let's imagine a bad
situation:

The user clicks the "Purchase", button which calls a `purchase` method that
spawns a network request and sends the `cart` array to the server. Because
of a bad network connection, the `purchase` method has to keep retrying the
request. Now, what if in the meantime the user accidentally clicks "Add to Cart"
button on an item they don't actually want before the network request begins?
If that happens and the network request begins, then that purchase method
will send the accidentally added item because it has a reference to a shopping
cart array that the `add_item_to_cart` method modified by adding an unwanted
item.

A great solution would be for the `add_item_to_cart` to always clone the `cart`,
edit it, and return the clone. This ensures that no other methods that are
holding onto a reference of the shopping cart will be affected by any changes.

Two caveats to mention to this approach:
  1. There might be cases where you actually want to modify the input object,
but when you adopt this programming practice you will find that those cases
are pretty rare. Most things can be refactored to have no side effects!

  2. Cloning big objects can be very expensive in terms of performance. Luckily,
this isn't a big issue in practice because there are
[great gems](https://github.com/hamstergem/hamster) that allow
this kind of programming approach to be fast and not as memory intensive as
it would be for you to manually clone objects and arrays.

**나쁨:**
```ruby
def add_item_to_cart(cart, item)
  cart.push(item: item, time: Time.now)
end
```

**좋음:**
```ruby
def add_item_to_cart(cart, item)
  cart + [{ item: item, time: Time.now }]
end
```
**[⬆ 맨 위로 이동](#목차)**

### Favor functional programming over imperative programming
Ruby isn't a functional language in the way that Haskell is, but it has
a functional flavor to it. Functional languages are cleaner and easier to test.
Favor this style of programming when you can.

**나쁨:**
```ruby
programmer_output = [
  {
    name: 'Uncle Bobby',
    lines_of_code: 500
  }, {
    name: 'Suzie Q',
    lines_of_code: 1500
  }, {
    name: 'Jimmy Gosling',
    lines_of_code: 150
  }, {
    name: 'Grace Hopper',
    lines_of_code: 1000
  }
]

total_output = 0

programmer_output.each do |output|
  total_output += output[:lines_of_code]
end
```

**좋음:**
```ruby
programmer_output = [
  {
    name: 'Uncle Bobby',
    lines_of_code: 500
  }, {
    name: 'Suzie Q',
    lines_of_code: 1500
  }, {
    name: 'Jimmy Gosling',
    lines_of_code: 150
  }, {
    name: 'Grace Hopper',
    lines_of_code: 1000
  }
]

INITIAL_VALUE = 0

total_output = programmer_output.sum(INITIAL_VALUE) { |output| output[:lines_of_code] }
```
**[⬆ 맨 위로 이동](#목차)**

### Encapsulate conditionals

**나쁨:**
```ruby
if params[:message].present? && params[:recipient].present?
  # ...
end
```

**좋음:**
```ruby
def send_message?(params)
  params[:message].present? && params[:recipient].present?
end

if send_message?(params)
  # ...
end
```
**[⬆ 맨 위로 이동](#목차)**

### Avoid negative conditionals

**나쁨:**
```ruby
if !genres.blank?
  # ...
end
```

**좋음:**
```ruby
unless genres.blank?
  # ...
end

# or

if genres.present?
  # ...
end
```
**[⬆ 맨 위로 이동](#목차)**

### Avoid conditionals
This seems like an impossible task. Upon first hearing this, most people say,
"how am I supposed to do anything without an `if` statement?" The answer is that
you can use polymorphism to achieve the same task in many cases. The second
question is usually, "well that's great but why would I want to do that?" The
answer is a previous clean code concept we learned: a method should only do
one thing. When you have classes and methods that have `if` statements, you
are telling your user that your method does more than one thing. Remember,
just do one thing.

**나쁨:**
```ruby
class Airplane
  # ...
  def cruising_altitude
    case @type
    when '777'
      max_altitude - passenger_count
    when 'Air Force One'
      max_altitude
    when 'Cessna'
      max_altitude - fuel_expenditure
    end
  end
end
```

**좋음:**
```ruby
class Airplane
  # ...
end

class Boeing777 < Airplane
  # ...
  def cruising_altitude
    max_altitude - passenger_count
  end
end

class AirForceOne < Airplane
  # ...
  def cruising_altitude
    max_altitude
  end
end

class Cessna < Airplane
  # ...
  def cruising_altitude
    max_altitude - fuel_expenditure
  end
end
```
**[⬆ 맨 위로 이동](#목차)**

### Avoid type-checking (part 1)
Ruby is dynamically typed, which means your methods can take any type of argument.
Sometimes you are bitten by this freedom and it becomes tempting to do
type-checking in your methods. There are many ways to avoid having to do this.
The first thing to consider is consistent APIs.

**나쁨:**
```ruby
def travel_to_texas(vehicle)
  if vehicle.is_a?(Bicycle)
    vehicle.pedal(@current_location, Location.new('texas'))
  elsif vehicle.is_a?(Car)
    vehicle.drive(@current_location, Location.new('texas'))
  end
end
```

**좋음:**
```ruby
def travel_to_texas(vehicle)
  vehicle.move(@current_location, Location.new('texas'))
end
```
**[⬆ 맨 위로 이동](#목차)**

### Avoid type-checking (part 2)
If you are working with basic values like strings and integers,
and you can't use polymorphism but you still feel the need to type-check,
you should consider using [contracts.ruby](https://github.com/egonSchiele/contracts.ruby). The problem with manually type-checking Ruby is that
doing it well requires so much extra verbiage that the faux "type-safety" you get
doesn't make up for the lost readability. Keep your Ruby clean, write
good tests, and have good code reviews.

**나쁨:**
```ruby
def combine(val1, val2)
  if (val1.is_a?(Numeric) && val2.is_a?(Numeric)) ||
     (val1.is_a?(String) && val2.is_a?(String))
    return val1 + val2
  end

  raise 'Must be of type String or Numeric'
end
```

**좋음:**
```ruby
def combine(val1, val2)
  val1 + val2
end
```
**[⬆ 맨 위로 이동](#목차)**

### Remove dead code
Dead code is just as bad as duplicate code. There's no reason to keep it in
your codebase. If it's not being called, get rid of it! It will still be safe
in your version history if you still need it.

**나쁨:**
```ruby
def old_request_module(url)
  # ...
end

def new_request_module(url)
  # ...
end

req = new_request_module(request_url)
inventory_tracker('apples', req, 'www.inventory-awesome.io')
```

**좋음:**
```ruby
def new_request_module(url)
  # ...
end

req = new_request_module(request_url)
inventory_tracker('apples', req, 'www.inventory-awesome.io')
```
**[⬆ 맨 위로 이동](#목차)**

## **Objects and Data Structures**
### Use getters and setters
Using getters and setters to access data on objects could be better than simply
looking for a property on an object. "Why?" you might ask. Well, here's an
unorganized list of reasons why:

* When you want to do more beyond getting an object property, you don't have
to look up and change every accessor in your codebase.
* Makes adding validation simple when doing a `set`.
* Encapsulates the internal representation.
* Easy to add logging and error handling when getting and setting.
* You can lazy load your object's properties, let's say getting it from a
server.


**나쁨:**
```ruby
def make_bank_account
  # ...

  {
    balance: 0
    # ...
  }
end

account = make_bank_account
account[:balance] = 100
account[:balance] # => 100
```

**좋음:**
```ruby
class BankAccount
  def initialize
    # this one is private
    @balance = 0
  end

  # a "getter" via a public instance method
  def balance
    # do some logging
    @balance
  end

  # a "setter" via a public instance method
  def balance=(amount)
    # do some logging
    # do some validation
    @balance = amount
  end
end

account = BankAccount.new
account.balance = 100
account.balance # => 100
```

Alternatively, if your getters and setters are absolutely trivial, you should use `attr_accessor` to define them. This is especially convenient for implementing data-like objects which expose data to other parts of the system (e.g., ActiveRecord objects, response wrappers for remote APIs).

**좋음:**
```ruby
class Toy
  attr_accessor :price
end

toy = Toy.new
toy.price = 50
toy.price # => 50
```

However, you have to be aware that in some situations, using `attr_accessor` is a code smell, read more [here](http://solnic.eu/2012/04/04/get-rid-of-that-code-smell-attributes.html).

**[⬆ 맨 위로 이동](#목차)**


## **Classes**

### Avoid fluent interfaces
A [Fluent interface](https://en.wikipedia.org/wiki/Fluent_interface) is an object
oriented API that aims to improve the readability of the source code by using
[method chaining](https://en.wikipedia.org/wiki/Method_chaining).

While there can be some contexts, frequently builder objects, where this
pattern reduces the verbosity of the code (e.g., ActiveRecord queries),
more often it comes at some costs:

1. Breaks [Encapsulation](https://en.wikipedia.org/wiki/Encapsulation_%28object-oriented_programming%29)
2. Breaks [Decorators](https://en.wikipedia.org/wiki/Decorator_pattern)
3. Is harder to [mock](https://en.wikipedia.org/wiki/Mock_object) in a test suite
4. Makes diffs of commits harder to read

For more information you can read the full [blog post](https://ocramius.github.io/blog/fluent-interfaces-are-evil/)
on this topic written by [Marco Pivetta](https://github.com/Ocramius).

**나쁨:**
```ruby
class Car
  def initialize(make, model, color)
    @make = make
    @model = model
    @color = color
    # NOTE: Returning self for chaining
    self
  end

  def set_make(make)
    @make = make
    # NOTE: Returning self for chaining
    self
  end

  def set_model(model)
    @model = model
    # NOTE: Returning self for chaining
    self
  end

  def set_color(color)
    @color = color
    # NOTE: Returning self for chaining
    self
  end

  def save
    # save object...
    # NOTE: Returning self for chaining
    self
  end
end

car = Car.new('Ford','F-150','red')
  .set_color('pink')
  .save
```

**좋음:**
```ruby
class Car
  attr_accessor :make, :model, :color

  def initialize(make, model, color)
    @make = make
    @model = model
    @color = color
  end

  def save
    # Save object...
  end
end

car = Car.new('Ford', 'F-150', 'red')
car.color = 'pink'
car.save
```
**[⬆ 맨 위로 이동](#목차)**

### Prefer composition over inheritance
As stated famously in [*Design Patterns*](https://en.wikipedia.org/wiki/Design_Patterns) by the Gang of Four,
you should prefer composition over inheritance where you can. There are lots of
good reasons to use inheritance and lots of good reasons to use composition.
The main point for this maxim is that if your mind instinctively goes for
inheritance, try to think if composition could model your problem better. In some
cases, it can.

You might be wondering then, "when should I use inheritance?" It
depends on your problem at hand, but this is a decent list of when inheritance
makes more sense than composition:

1. Your inheritance represents an "is-a" relationship and not a "has-a"
relationship (Human->Animal vs. User->UserDetails).
2. You can reuse code from the base classes (Humans can move like all animals).
3. You want to make global changes to derived classes by changing a base class.
(Change the caloric expenditure of all animals when they move).

**나쁨:**
```ruby
class Employee
  def initialize(name, email)
    @name = name
    @email = email
  end

  # ...
end

# Bad because Employees "have" tax data. EmployeeTaxData is not a type of Employee
class EmployeeTaxData < Employee
  def initialize(ssn, salary)
    super()
    @ssn = ssn
    @salary = salary
  end

  # ...
end
```

**좋음:**
```ruby
class EmployeeTaxData
  def initialize(ssn, salary)
    @ssn = ssn
    @salary = salary
  end

  # ...
end

class Employee
  def initialize(name, email)
    @name = name
    @email = email
  end

  def set_tax_data(ssn, salary)
    @tax_data = EmployeeTaxData.new(ssn, salary)
  end
  # ...
end
```
**[⬆ 맨 위로 이동](#목차)**

## **SOLID**
### Single Responsibility Principle (SRP)
As stated in Clean Code, "There should never be more than one reason for a class
to change". It's tempting to jam-pack a class with a lot of functionality, like
when you can only take one suitcase on your flight. The issue with this is
that your class won't be conceptually cohesive and it will give it many reasons
to change. Minimizing the number of times you need to change a class is important.
It's important because if too much functionality is in one class and you modify
a piece of it, it can be difficult to understand how that will affect other
dependent modules in your codebase.

**나쁨:**
```ruby
class UserSettings
  def initialize(user)
    @user = user
  end

  def change_settings(settings)
    return unless valid_credentials?
    # ...
  end

  def valid_credentials?
    # ...
  end
end
```

**좋음:**
```ruby
class UserAuth
  def initialize(user)
    @user = user
  end

  def valid_credentials?
    # ...
  end
end

class UserSettings
  def initialize(user)
    @user = user
    @auth = UserAuth.new(user)
  end

  def change_settings(settings)
    return unless @auth.valid_credentials?
    # ...
  end
end
```
**[⬆ 맨 위로 이동](#목차)**

### Open/Closed Principle (OCP)
As stated by [Bertrand Meyer](https://en.wikipedia.org/wiki/Bertrand_Meyer), "software entities (classes, modules, functions,
etc.) should be open for extension, but closed for modification." What does that
mean though? This principle basically states that you should allow users to
add new functionalities without changing existing code.

**나쁨:**
```ruby
class Adapter
  attr_reader :name
end

class AjaxAdapter < Adapter
  def initialize
    super()
    @name = 'ajaxAdapter'
  end
end

class NodeAdapter < Adapter
  def initialize
    super()
    @name = 'nodeAdapter'
  end
end

class HttpRequester
  def initialize(adapter)
    @adapter = adapter
  end

  def fetch(url)
    case @adapter.name
    when 'ajaxAdapter'
      make_ajax_call(url)
    when 'nodeAdapter'
      make_http_call(url)
    end
  end

  def make_ajax_call(url)
    # ...
  end

  def make_http_call(url)
    # ...
  end
end
```

**좋음:**
```ruby
class Adapter
  attr_reader :name
end

class AjaxAdapter < Adapter
  def initialize
    super()
    @name = 'ajaxAdapter'
  end

  def request(url)
    # ...
  end
end

class NodeAdapter < Adapter
  def initialize
    super()
    @name = 'nodeAdapter'
  end

  def request(url)
    # ...
  end
end

class HttpRequester
  def initialize(adapter)
    @adapter = adapter
  end

  def fetch(url)
    @adapter.request(url)
  end
end
```
**[⬆ 맨 위로 이동](#목차)**

### Liskov Substitution Principle (LSP)
This is a scary term for a very simple concept. It's formally defined as "If S
is a subtype of T, then objects of type T may be replaced with objects of type S
(i.e., objects of type S may substitute objects of type T) without altering any
of the desirable properties of that program (correctness, task performed,
etc.)." That's an even scarier definition.

The best explanation for this is if you have a parent class and a child class,
then the base class can always be replaced by the child class without getting
incorrect results. This might still be confusing, so let's take a look at the
classic Square-Rectangle example. Mathematically, a square is a rectangle, but
if you model it using the "is-a" relationship via inheritance, you quickly
get into trouble.

**나쁨:**
```ruby
class Rectangle
  def initialize
    @width = 0
    @height = 0
  end

  def color=(color)
    # ...
  end

  def render(area)
    # ...
  end

  def width=(width)
    @width = width
  end

  def height=(height)
    @height = height
  end

  def area
    @width * @height
  end
end

class Square < Rectangle
  def width=(width)
    @width = width
    @height = width
  end

  def height=(height)
    @width = height
    @height = height
  end
end

def render_large_rectangles(rectangles)
  rectangles.each do |rectangle|
    rectangle.width = 4
    rectangle.height = 5
    area = rectangle.area # BAD: Returns 25 for Square. Should be 20.
    rectangle.render(area)
  end
end

rectangles = [Rectangle.new, Rectangle.new, Square.new]
render_large_rectangles(rectangles)
```

**좋음:**
```ruby
class Shape
  def color=(color)
    # ...
  end

  def render(area)
    # ...
  end
end

class Rectangle < Shape
  def initialize(width, height)
    super()
    @width = width
    @height = height
  end

  def area
    @width * @height
  end
end

class Square < Shape
  def initialize(length)
    super()
    @length = length
  end

  def area
    @length * @length
  end
end

def render_large_shapes(shapes)
  shapes.each do |shape|
    area = shape.area
    shape.render(area)
  end
end

shapes = [Rectangle.new(4, 5), Rectangle.new(4, 5), Square.new(5)]
render_large_shapes(shapes)
```
**[⬆ 맨 위로 이동](#목차)**

### Interface Segregation Principle (ISP)
Ruby doesn't have interfaces so this principle doesn't apply as strictly
as others. However, it's important and relevant even with Ruby's lack of
type system.

ISP states that "Clients should not be forced to depend upon interfaces that
they do not use." Interfaces are implicit contracts in Ruby because of
duck typing.

When a client depends upon a class that contains interfaces that the client does not use, but that other clients do use, then that client will be affected by the changes that those other clients force upon the class.

The following example is taken from [here](http://geekhmer.github.io/blog/2015/03/18/interface-segregation-principle-in-ruby/).

**나쁨:**
```ruby
class Car
  # used by Driver
  def open
    # ...
  end

  # used by Driver
  def start_engine
    # ...
  end

  # used by Mechanic
  def change_engine
    # ...
  end
end

class Driver
  def drive
    @car.open
    @car.start_engine
  end
end

class Mechanic
  def do_stuff
    @car.change_engine
  end
end

```

**좋음:**
```ruby
# used by Driver only
class Car
  def open
    # ...
  end

  def start_engine
    # ...
  end
end

# used by Mechanic only
class CarInternals
  def change_engine
    # ...
  end
end

class Driver
  def drive
    @car.open
    @car.start_engine
  end
end

class Mechanic
  def do_stuff
    @car_internals.change_engine
  end
end
```

**[⬆ 맨 위로 이동](#목차)**

### Dependency Inversion Principle (DIP)
This principle states two essential things:
1. High-level modules should not depend on low-level modules. Both should
depend on abstractions.
2. Abstractions should not depend upon details. Details should depend on
abstractions.

Simply put, DIP keeps high-level
modules from knowing the details of its low-level modules and setting them up.
It can accomplish this through dependency injection. A huge benefit of this is that
it reduces the coupling between modules. Coupling is a very bad development pattern
because it makes your code hard to refactor.

As stated previously, Ruby doesn't have interfaces so the abstractions
that are depended upon are implicit contracts. That is to say, the methods
and properties that an object/class exposes to another object/class. In the
example below, the implicit contract is that any Request module for an
`InventoryTracker` will have a `request_items` method.

**나쁨:**
```ruby
class InventoryRequester
  def initialize
    @req_methods = ['HTTP']
  end

  def request_item(item)
    # ...
  end
end

class InventoryTracker
  def initialize(items)
    @items = items

    # BAD: We have created a dependency on a specific request implementation.
    @requester = InventoryRequester.new
  end

  def request_items
    @items.each do |item|
      @requester.request_item(item)
    end
  end
end

inventory_tracker = InventoryTracker.new(['apples', 'bananas'])
inventory_tracker.request_items
```

**좋음:**
```ruby
class InventoryTracker
  def initialize(items, requester)
    @items = items
    @requester = requester
  end

  def request_items
    @items.each do |item|
      @requester.request_item(item)
    end
  end
end

class InventoryRequesterV1
  def initialize
    @req_methods = ['HTTP']
  end

  def request_item(item)
    # ...
  end
end

class InventoryRequesterV2
  def initialize
    @req_methods = ['WS']
  end

  def request_item(item)
    # ...
  end
end

# By constructing our dependencies externally and injecting them, we can easily
# substitute our request module for a fancy new one that uses WebSockets.
inventory_tracker = InventoryTracker.new(['apples', 'bananas'], InventoryRequesterV2.new)
inventory_tracker.request_items
```
**[⬆ 맨 위로 이동](#목차)**

## **Testing**
Testing is more important than shipping. If you have no tests or an
inadequate amount, then every time you ship code you won't be sure that you
didn't break anything. Deciding on what constitutes an adequate amount is up
to your team, but having 100% coverage (all statements and branches) is how
you achieve very high confidence and developer peace of mind. This means that
in addition to having a [great testing framework](http://rspec.info/), you also need to use a
[good coverage tool](https://coveralls.io/).

There's no excuse to not write tests. Aim to always write tests
for every new feature/module you introduce. If your preferred method is
Test Driven Development (TDD), that is great, but the main point is to just
make sure you are reaching your coverage goals before launching any feature,
or refactoring an existing one.

### Single expectation per test

**나쁨:**
```ruby
require 'rspec'

describe 'Calculator' do
  let(:calculator) { Calculator.new }

  it 'performs addition, subtraction, multiplication and division' do
    expect(calculator.calculate('1 + 2')).to eq(3)
    expect(calculator.calculate('4 - 2')).to eq(2)
    expect(calculator.calculate('2 * 3')).to eq(6)
    expect(calculator.calculate('6 / 2')).to eq(3)
  end
end
```

**좋음:**
```ruby
require 'rspec'

describe 'Calculator' do
  let(:calculator) { Calculator.new }

  it 'performs addition' do
    expect(calculator.calculate('1 + 2')).to eq(3)
  end

  it 'performs subtraction' do
    expect(calculator.calculate('4 - 2')).to eq(2)
  end

  it 'performs multiplication' do
    expect(calculator.calculate('2 * 3')).to eq(6)
  end

  it 'performs division' do
    expect(calculator.calculate('6 / 2')).to eq(3)
  end
end
```
**[⬆ 맨 위로 이동](#목차)**

## **Error Handling**
Thrown errors are a good thing! They mean the runtime has successfully
identified when something in your program has gone wrong and it's letting
you know by stopping method execution on the current stack, killing the
process, and notifying you in the logs with a stack trace.

### Don't ignore caught errors
Doing nothing with a caught error doesn't give you the ability to ever fix
or react to said error. Logging the error
isn't much better as often times it can get lost in a sea of other logs. If you wrap any bit of code in a `begin/rescue` it means you
think an error may occur there and therefore you should have a plan,
or create a code path, for when it occurs.

**나쁨:**
```ruby
require 'logger'

logger = Logger.new(STDOUT)

begin
  method_that_might_throw()
rescue StandardError => err
  logger.info(err)
end
```

**좋음:**
```ruby
require 'logger'

logger = Logger.new(STDOUT)
# Change the logger level to ERROR to output only logs with ERROR level and above
logger.level = Logger::ERROR

begin
  method_that_might_throw()
rescue StandardError => err
  # Option 1: Only log errors
  logger.error(err)
  # Option 2: Notify end-user via an interface
  notify_user_of_error(err)
  # Option 3: Report error to a third-party service like Honeybadger
  report_error_to_service(err)
  # OR do all three!
end
```

### Provide context with exceptions
Use a descriptive error class name and a message when you raise an error. That way you know why the error occurred and you can rescue the specific type of error.

***나쁨:***
```ruby
def initialize(user)
  fail unless user
  ...
end
```

***좋음:***
```ruby
def initialize(user)
  fail ArgumentError, 'Missing user' unless user
  ...
end
```

**[⬆ 맨 위로 이동](#목차)**


## **Formatting**
Formatting is subjective. Like many rules herein, there is no hard and fast
rule that you must follow. The main point is DO NOT ARGUE over formatting.
There are tons of tools like [RuboCop](https://github.com/bbatsov/rubocop) to automate this.
Use one! It's a waste of time and money for engineers to argue over formatting.

For things that don't fall under the purview of automatic formatting
(indentation, tabs vs. spaces, double vs. single quotes, etc.) look here
for some guidance.

### Use consistent capitalization
Ruby is dynamically typed, so capitalization tells you a lot about your variables,
methods, etc. These rules are subjective, so your team can choose whatever
they want. The point is, no matter what you all choose, just be consistent.

**나쁨:**
```ruby
DAYS_IN_WEEK = 7
daysInMonth = 30

songs = ['Back In Black', 'Stairway to Heaven', 'Hey Jude']
Artists = ['ACDC', 'Led Zeppelin', 'The Beatles']

def eraseDatabase; end

def restore_database; end

class ANIMAL; end
class Alpaca; end
```

**좋음:**
```ruby
DAYS_IN_WEEK = 7
DAYS_IN_MONTH = 30

SONGS = ['Back In Black', 'Stairway to Heaven', 'Hey Jude'].freeze
ARTISTS = ['ACDC', 'Led Zeppelin', 'The Beatles'].freeze

def erase_database; end

def restore_database; end

class Animal; end
class Alpaca; end
```
**[⬆ 맨 위로 이동](#목차)**


### Method callers and callees should be close
If a method calls another, keep those methods vertically close in the source
file. Ideally, keep the caller right above the callee. We tend to read code from
top-to-bottom, like a newspaper. Because of this, make your code read that way.

**나쁨:**
```ruby
class PerformanceReview
  def initialize(employee)
    @employee = employee
  end

  def lookup_peers
    db.lookup(@employee, :peers)
  end

  def lookup_manager
    db.lookup(@employee, :manager)
  end

  def peer_reviews
    peers = lookup_peers
    # ...
  end

  def perf_review
    peer_reviews
    manager_review
    self_review
  end

  def manager_review
    manager = lookup_manager
    # ...
  end

  def self_review
    # ...
  end
end

review = PerformanceReview.new(employee)
review.perf_review
```

**좋음:**
```ruby
class PerformanceReview
  def initialize(employee)
    @employee = employee
  end

  def perf_review
    peer_reviews
    manager_review
    self_review
  end

  def peer_reviews
    peers = lookup_peers
    # ...
  end

  def lookup_peers
    db.lookup(@employee, :peers)
  end

  def manager_review
    manager = lookup_manager
    # ...
  end

  def lookup_manager
    db.lookup(@employee, :manager)
  end

  def self_review
    # ...
  end
end

review = PerformanceReview.new(employee)
review.perf_review
```

**[⬆ 맨 위로 이동](#목차)**

## **Comments**


### Don't leave commented out code in your codebase
Version control exists for a reason. Leave old code in your history.

**나쁨:**
```ruby
do_stuff
# do_other_stuff
# do_some_more_stuff
# do_so_much_stuff
```

**좋음:**
```ruby
do_stuff
```
**[⬆ 맨 위로 이동](#목차)**

### Don't have journal comments
Remember, use version control! There's no need for dead code, commented code,
and especially journal comments. Use `git log` to get history!

**나쁨:**
```ruby
# 2016-12-20: Removed monads, didn't understand them (RM)
# 2016-10-01: Improved using special monads (JP)
# 2016-02-03: Removed type-checking (LI)
# 2015-03-14: Added combine with type-checking (JR)
def combine(a, b)
  a + b
end
```

**좋음:**
```ruby
def combine(a, b)
  a + b
end
```
**[⬆ 맨 위로 이동](#목차)**

## Translations

This is also available in other languages:

  - [Brazilian Portuguese](https://github.com/uohzxela/clean-code-ruby/blob/master/translations/pt-BR.md)
  - [Simplified Chinese](https://github.com/uohzxela/clean-code-ruby/blob/master/translations/zh-CN.md)

**[⬆ 맨 위로 이동](#목차)**
