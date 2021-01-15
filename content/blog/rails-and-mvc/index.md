---
title: "Ruby on Rails and MVC"
date: "2020-01-15T02:01:01.284Z"
category: "dev"
description: "Ruby on Rails and MVC"
emoji: "👓"
---

M = Model
V = View
C = Controller

Modeling means finding the concepts needed for the problem the system solves, giving them names, and organizing their relationships to each other.


Create a Rails project.

```ruby:title=shell
% rails new book_admin \
> --skip-actiom-mailer \
> --skip-action-mailbox \
> --skip-action-text \
> --skip-active-storage \
> --skip-action-cable
```


Create a model.

```ruby:title=shell
% bin/rails g model Book \
> name:string \
> published_on:date \
> price:integer
```

Reflect the contents of the migration in the DB.

```ruby:title=shell
% bin/rails db:create
% bin/rails db:migrate
```

Check the defined fields.

```ruby:title=shell
% bin/rails c
Running via Spring preloader in process 73176
Loading development environment (Rails 6.1.1)
irb(main):001:0> Book.new
   (0.3ms)  SELECT sqlite_version(*)
=> #<Book id: nil, name: nil, published_on: nil, price: nil, created_at: nil, updated_at: nil>
irb(main):002:0> Book
=> Book(id: integer, name: string, published_on: date, price: integer, created_at: datetime, updated_at: datetime)
```

Entering data.

```ruby:title=shell
irb(main):006:0> (1..5).each do |i|
irb(main):007:1*   Book.create(
irb(main):008:2*     name: "Book #{i}",
irb(main):009:2*     published_on: Time.parse("20210115").ago(i.months),
irb(main):010:2*     price: (i * 1000),
irb(main):011:2*   )
irb(main):012:1> end
```

Search for records.

```ruby:title=shell
irb(main):014:0> Book.find(1)
  Book Load (0.4ms)  SELECT "books".* FROM "books" WHERE "books"."id" = ? LIMIT ?  [["id", 1], ["LIMIT", 1]]
=> #<Book id: 1, name: "Book 1", published_on: "2020-12-15", price: 1000, created_at: "2021-01-14 23:38:47.008968000 +0000", updated_at: "2021-01-14 23:38:47.008968000 +0000">

irb(main):017:0> Book.find_by(name: "Book 3")
  Book Load (0.4ms)  SELECT "books".* FROM "books" WHERE "books"."name" = ? LIMIT ?  [["name", "Book 3"], ["LIMIT", 1]]
=> #<Book id: 3, name: "Book 3", published_on: "2020-10-15", price: 3000, created_at: "2021-01-14 23:38:47.029707000 +0000", updated_at: "2021-01-14 23:38:47.029707000 +0000">
irb(main):018:0> Book.find_by(price: 3000)
  Book Load (0.3ms)  SELECT "books".* FROM "books" WHERE "books"."price" = ? LIMIT ?  [["price", 3000], ["LIMIT", 1]]
=> #<Book id: 3, name: "Book 3", published_on: "2020-10-15", price: 3000, created_at: "2021-01-14 23:38:47.029707000 +0000", updated_at: "2021-01-14 23:38:47.029707000 +0000">
```

Search for multiple records.

```ruby:title=shell
irb(main):024:0> Book.where("price > ?", 3000)
  Book Load (0.3ms)  SELECT "books".* FROM "books" WHERE (price > 3000) /* loading for inspect */ LIMIT ?  [["LIMIT", 11]]
=> #<ActiveRecord::Relation [#<Book id: 4, name: "Book 4", published_on: "2020-09-15", price: 4000, created_at: "2021-01-14 23:38:47.033181000 +0000", updated_at: "2021-01-14 23:38:47.033181000 +0000">, #<Book id: 5, name: "Book 5", published_on: "2020-08-15", price: 5000, created_at: "2021-01-14 23:38:47.037392000 +0000", updated_at: "2021-01-14 23:38:47.037392000 +0000">]>

irb(main):027:0> Book.where('price > ?', 3000).limit(3).order(:name)
  Book Load (0.5ms)  SELECT "books".* FROM "books" WHERE (price > 3000) /* loading for inspect */ ORDER BY "books"."name" ASC LIMIT ?  [["LIMIT", 3]]
=> #<ActiveRecord::Relation [#<Book id: 4, name: "Book 4", published_on: "2020-09-15", price: 4000, created_at: "2021-01-14 23:38:47.033181000 +0000", updated_at: "2021-01-14 23:38:47.033181000 +0000">, #<Book id: 5, name: "Book 5", published_on: "2020-08-15", price: 5000, created_at: "2021-01-14 23:38:47.037392000 +0000", updated_at: "2021-01-14 23:38:47.037392000 +0000">]>
```

