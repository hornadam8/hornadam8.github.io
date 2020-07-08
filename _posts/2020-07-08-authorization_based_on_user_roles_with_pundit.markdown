---
layout: post
title:      "Authorization based on User Roles with Pundit "
date:       2020-07-08 11:59:28 +0000
permalink:  authorization_based_on_user_roles_with_pundit
---

When creating a new rails application with user roles and authentication, Pundit can help handle authorizing actions in individual controllers. After following the set up [here](https://github.com/varvet/pundit), You can begin by adding a "current_role" attribute to your users table, this will help later in determining whether a user is authorized to perform and action. 

```
Class AddCurrentRoleToUsers < ActiveRecord::Migration[6.0]
     def change
		 add_column :users, :current_role, :string
     end
end
```

Additionally, you can set user validations to make sure the user has a role:

```
Class User < ApplicationRecord
     ...
     validates :current_role, :inclusion {in: %w(Teacher Student)}
     ...
end
```

After/during login have the user set their current role, and Pundit Policies will handle authorization. In my example, teachers can create a class (called a cohort to avoid ruby naming issues), but a student cannot. To handle this rule, my cohort new and create policies look like this: 

```
class CohortPolicy < ApplicationPolicy
    attr_reader :user,:cohort

    def initialize(user,cohort)
        @user = user
        @cohort = cohort
    end

    def new?
        if @user.current_role == "Teacher"
            true
        end
    end

    def create?
        if @user.current_role == "Teacher"
            true
        end
    end
		
		...
		```
		
		Pundit policies are initialized with a user and a "record", I chose to name the record after the class but that is optional.
		
		 Additionally, classes are only viewable by the teacher who created the class and the students who joined. In order to handle these conditions, the rest of my cohort policy looks like this:
		
    ```
    def edit?
        if @user.current_role == "Teacher" && @cohort.teacher == @user
          true
        end
    end

    def update?
      if @user.current_role == "Teacher" && @cohort.teacher == @user
        true
      end
    end

    def show?
      if (@user.current_role == "Teacher" && @user == @cohort.teacher) ||
        @cohort.students.include?(@user) && @user.current_role == "Student"
        true
      end
    end

    def index?
      if @user.current_role == "Student"
        true
      end
    end

    def destroy?
      if @user.current_role == "Teacher" && @user == @cohort.teacher
        true
      end
    end

end
```

The last thing to do is set up authorizations in you controllers. In this example, the easiest way would be to call 'authorize @cohort' within the controller, but since Pundit throws an error if it cant authorize, I think having helper methods cleans it up significantly. In the application controller, I included this:

```
class ApplicationController < ActionController::Base
    include Pundit

    rescue_from Pundit::NotAuthorizedError, with: :user_not_authorized
		
		private
		
		def user_not_authorized
        flash[:alert] = "You are not authorized to perform this action."
        redirect_to(request.referrer || user_path(current_user))
    end
		
	end
```

This will make sure the user is redirected if they arent authorized rather than break the application. Last but not least, I added a before action in my cohorts controller to make sure authorization occured immediately after setting the cohort: 

```
class CohortsController < ApplicationController

    before_action :check_user
    before_action :set_cohort, only: [:show,:edit,:update,:destroy]
    before_action :authorize!, only: [:show,:edit,:update,:destroy]


  private

    def authorize!
      authorize @cohort
    end
```

That's pretty much it! Just set up policies and check in the controllers and let Pundit do the rest.
