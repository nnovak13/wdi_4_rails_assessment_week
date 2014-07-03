# Week 4 Comprehensive Assessment

Fork this repository, update this file to include your answers, and submit a pull request. You may refer to any notes, past projects, or external resources you want. The questions do not have to be completed in order.

1. My app has Students who are organized into Houses. What lines should I add to `config/routes.rb` to create a complete set of nested RESTful routes for these resources?

 resources :houses do
  resources :students
  end

root 'houses#index'






2. Write a migration to create a `students` table with columns for name, age, and something to link the student to the `House` they belong to.


rails g migration CreateStudents

class CreateStudents < ActiveRecord::Migration
  def change
    create_table :students do |t|
      t.references :house, index: true
      t.string :name
      t.integer :age
    end
  end
end


rake db:migrate





3. Assuming a Student can only be in a single House, what kind of associations should I define on these models? Do I need to introduce any new models, and if so, what associations should I define on those?


create a house.rb in app/models

classStudent <ActiveRecord::Base

  has_many :students
end




create student.rb in app/models

classStudent <ActiveRecord::Base

  belongs_to :house

end







4. Now I want Students to be able to enroll in Courses. What kind of associations should I define on these models? Do I need to introduce any new models, and if so, what associations should I define on those?



rails g migration CreateCourses

class CreateCourses < ActiveRecord::Migration
  def change
    create_table :courses do |t|
      t.string :name
      t.references :courseable, polymorphic: true, index: true
    end
  end
end




create course.rb in app/models

classCourse <ActiveRecord::Base

belongs_to :courseable, polymorphic: true

end




adjust student.rb in app/models

classStudent <ActiveRecord::Base

  belongs_to :house
  has_many courses

end





rake db:migrate
rake db:seed




5. I want Students to be able to leave Comments on both Courses and Professors. What kind of associations should I define on these models? Do I need to introduce any new models, and if so, what associations should I define on those?


rails g migration CreateProfessors


class CreateProfessors < ActiveRecord::Migration
  def change
    create_table :students do |t|
      t.string :name
    end
  end
end

rake db:migrate

MODEL

class Professor < ActiveRecord::Base
  has_many :courses
end





rails g migration CreateComments



class CreateComments < ActiveRecord::Migration
  def change
    create_table :comments do |t|
      t.references :student, index: true
      t.references :commentable, polymorphic: true, index: true
      t.string :body
      t.timestamps
    end
  end
end


MODEL

class Comment < ActiveRecord::Base
  belongs_to :student
  belongs_to :commentable, polymorphic: true
  validates :student, :commentable, :body, presence: true

end



6. When creating a new Student in my students controller, assuming I have a `student_params` method and the house the student should belong to is stored in `@house`, what code should I write to link the student to the house?





7. If I'm using Devise with a User model, what code should I write in a controller or view to find out whether a user is signed in? And how can I get a reference to the currently-signed-in user?


  before_action :authenticate_user!

   @student.user = current_user


8. If I'm using Devise with a User model, what code should I write in my students controller so that only signed-in users can access the `new` and `create` actions?

  before_action :authenticate_user!


def new
    @student = Student.new
  end

  def create
    @student = Student.new(student_params)

    #as long as user is signed in..
    @student.user = current_user


    if @student.save
      redirect_to @student
    else
      flash.now[:alert] = @student.errors.full_messages.join(', ')
      render :new
    end
  end







9. When I deploy a new version of my app to Heroku that contains a new migration, how can I run that migration on my production database?


push heroku:master

10. If accessing my app on Heroku gives me an uninformative "something went wrong" error message, how can I find out what the real error is?


heroku logs
