### RSpec
A testing framework with rich DSL and fully BDD, to understand why RSpec is the way it is, we need to understand the point of BDD and its parent, TDD.

1. Write the smallest possible test case that matches what we need to program.
1. Run the test and watch it fail. This gets you into thinking how to write only the code that makes it pass.
1. Write some code with the goal of making the test pass.
Run your test suite. Repeat steps 3 and 4 until all tests pass.
1. Go back and refactor your new code, making it as simple and clear as possible while keeping the test suite green.
---
### Whats wrong with our tests?

One of the worst parts of writing tests or debugging failures is when reading the tests takes more effort than writing the implementation. RSpec has many tools that allow you to keep tests terse, and to the point.

---

### How to run tests
Head on over to [Ruby Coding Standards](http://woki/display/devstds/Ruby+Coding+Standards) to and set up your project with a Rakefile in which you can set up some rake tasks for unit, medium and acceptance tests.

---

### First Example
```ruby
describe Order do
  describe "#submit" do
    let(:title_name) { 'A great book title' }
    let(:book_price) { 20 }

    before(:each) do
      @book = Book.new(title: title_name, price: book_price)
      @order.submit
    end

    describe 'Argument validation' do
      describe 'When both title_name and book_price are valid values' do
        it 'Successfully saves book in database' do
          expect(@db.books).to include(title_name)
        end
      end

      describe "When title_name is not specified" do
        let(:title_name) { nil }
        it 'Will not save book' do
          expect(@db.books).not_to include(title_name)
        end
      end
    end
  end
end
```
---
* Very legible, filled with an extensive DSL with many third party libraries to extend functionality.
* `describe` - a way of declaring collections of tests, with the title being output on the console when tests are run.
* `it` - a single test case, normally setup for this test is wrapped up in the surrounding descibe block and the it block only tests the outcomes of the operation.
* `expect` - a DSL for defining the expected outcome of the operation.
* `let(:some_var_name) { some_var_value }` lets are used to set up variables that you want to override over multiple test cases. A let variable is lazily evaluated i.e. it is only evaluated when the value gets used. In the example above title_name is used with two different values and the resulting db is tested.
* `before` - a hook that allows you to run code before a test is evaluated, useful for setup of test cases.
---

### RSpec Matchers
* Equality/Identity Matchers - `expect(a).to eql "test string"`
* Comparison Matchers - `expect(b).to be <= b`
* Boolean Matchers - `expect(x).to be true`
* Error Matchers - `expect { method_call(wrong_val) }.to raise_error /extremely bad error/`
---

### RSpec Mocks
```ruby
class ClassRoom
   def initialize(students)
      @students = students
   end

   def list_student_names
      @students.map(&:name).join(',')
   end
end

describe ClassRoom do
   it 'the list_student_names method should work correctly' do
      student1 = double('student')
      student2 = double('student')

      allow(student1).to receive(:name) { 'John Smith'}
      allow(student2).to receive(:name) { 'Jill Smith'}

      cr = ClassRoom.new [student1,student2]
      expect(cr.list_student_names).to eq('John Smith,Jill Smith')
   end
end
```
---

### RSpec Tags
* Allows you to run specific tests according to a tag you mark the describe/it block

```ruby
describe "How to run specific Examples with Tags" do
  it 'is a slow test', slow: true do
    sleep 10
    puts 'This test is slow!'
  end

  it 'is a fast test', fast: true do
    puts 'This test is fast!'
  end
end
```
---

### RSpec Subject
* Shorthand for the class declared in the describe call.

```ruby
class Person
  attr_reader :first_name, :last_name

  def initialize(first_name, last_name)
    @first_name = first_name
    @last_name = last_name
  end
end

describe Person.new('John', 'Smith') do
   it { is_expected.to have_attributes(first_name: 'John') }
   it { is_expected.to have_attributes(last_name: 'Smith') }
end
```
---

### RSpec Metadata
* Contains information about current example_group, file_path, line number etc.
* Also contains tag data
```ruby
describe "An Example Group with a metadata variable", :foo => 17 do
  context 'and a context with another variable', :bar => 12 do
    it 'can access the metadata variable of the outer Example Group' do |example|
      expect(example.metadata[:foo]).to eq(17)
    end

    it 'can access the metadata variable in the context block' do |example|
      expect(example.metadata[:bar]).to eq(12)
    end
  end
end
```
---

#### Resources
##### Internal links
A great starting point is the Ruby Standards page which has info on setting up a project for testing and following coding best practices, also some known bugs and workarounds.
[Ruby Coding Standards](http://woki/display/devstds/Ruby+Coding+Standards)
https://stash/projects/OCD/repos/capistrano-smoke_test/browse

##### External Links
* [Official docs for RSpec](https://www.relishapp.com/rspec)
* [rspec.info](http://rspec.info/)
* [betterspecs.org](http://betterspecs.org/)
