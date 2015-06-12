[1, 2, 3, 4, 5]

取前几个元素
enum.first(n)
enum.take(n)

enum.any? { |n| n.odd? }
enum.any?(&:odd?)


enum.all?(&:odd?)

enum.none?(&:odd?)

enum.map { |n| n**2 }
enum.collect { |n| n**2 }

enum.select(&:even?)

enum.reject(&:even?)

enum.find(&:even?)
enum.detect(&:even?)

enum.inject([]) do |square_pairs, n|
  square_pairs << [n, n**2]
end

addresses.grep(valid_email_regex)

enum.partition(&:even?)

enum.zip(squares)

days.cycle(4) { |day| puts "Today is #{day}" }

The Ruby Object Model by Dave Thomas
https://www.youtube.com/watch?v=X2sgQ38UDVY
smalltalk best practice patterns

Ruby class is a object contains a group of methods

animal = "cat"
class << animal
  def speak
    puts "miao"
  end
end
class << animal 就是打开animal这个对象（实例）的sigleton class/meta class/eigen class，然后在里面定义方法

更实用，常见的场景是定义很多所谓的类方法。
class Dave
  class << self
    def speak
      puts 'Hello'
    end
  end
end
Dave.speak

Inherit from Expression

class Persion < Struct.new(:age, :name)
end

Include & Extend
  ruby创建了一个隐含的父类，然后把引用module的方法

self
right then up

Duck.ancestors

Class.instance_methods - Module.instance_methods

another very good video: https://www.youtube.com/watch?v=by5fFOBhtPQ


singleton class动态创建，生内存，防溢出

class << obj
  self
  # include Module
end

"abc".singleton_class
String.singleton_class
