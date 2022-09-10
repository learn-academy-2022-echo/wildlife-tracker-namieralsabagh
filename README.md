Wildlife Tracker Challenge The Forest Service is considering a proposal to place in conservancy a forest of virgin Douglas fir just outside of Portland, Oregon. Before they give the go ahead, they need to do an environmental impact study. They've asked you to build an API the rangers can use to report wildlife sightings.

Story 1: In order to track wildlife sightings, as a user of the API, I need to manage animals.

Setting up the Rails Application;

$ cd desktop
$ rails db:create
$ rails new wildlife-tracker -d postgresql -T
$ cd wildlife-tracker
$ git remote add origin https://github.com/learn-academy-2022-echo/wildlife-tracker-namieralsabagh.git
$ code .
$ git commit -m "Initial commit"
$ git push origin main
$ git checkout main
$ git pull origin main
$ git branch
$ git checkout -b animal-crud-actions
$ code .
$ bundle add rspec-rails
$ rails generate rspec:install
$ rails s


Branch: animal-crud-actions ✅

Acceptance Criteria

Create a resource for animal with the following information: common name and scientific binomial ✅

$ rails generate resource Animal common_name:string scientific_binomial:string
 invoke  active_record
      create    db/migrate/20220909182307_create_animals.rb
      create    app/models/animal.rb
      invoke    rspec
      create      spec/models/animal_spec.rb
      invoke  controller
      create    app/controllers/animals_controller.rb
      invoke    erb
      create      app/views/animals
      invoke    rspec
      create      spec/requests/animals_spec.rb
      invoke    helper
      create      app/helpers/animals_helper.rb
      invoke      rspec
      create        spec/helpers/animals_helper_spec.rb
      invoke  resource_route
       route    resources :animals

$ rails db:migrate

Can see the data response of all the animals ✅

$ rails routes -E
Prefix            | 
Verb              | GET
URI               | /rails/active_storage/representations/:signed_blob_id/:variation_key/*filename(.:format)
Controller#Action | active_storage/representations/redirect#show
--[ Route 36 ]------------------------------------------------------------------
Prefix            | rails_disk_service
Verb              | GET
URI               | /rails/active_storage/disk/:encoded_key/*filename(.:format)
Controller#Action | active_storage/disk#show
--[ Route 37 ]------------------------------------------------------------------
Prefix            | update_rails_disk_service
Verb              | PUT
URI               | /rails/active_storage/disk/:encoded_token(.:format)
Controller#Action | active_storage/disk#update
--[ Route 38 ]------------------------------------------------------------------
Prefix            | rails_direct_uploads
Verb              | POST
URI               | /rails/active_storage/direct_uploads(.:format)
Controller#Action | active_storage/direct_uploads#create


Can create a new animal in the database ✅

In rails console
$ Animal.create common_name: 'Crocodile', scientific_binomial: 'Crocodylidae'
$ Animal.create common_name: 'Tiger', scientific_binomial: 'Panthera tigris'

Can update an existing animal in the database ✅

def update
        animal = Animal.find(params[:id])
        animal.update(animal_params)
        if animal.valid?
            render json: animal
        else
            render json: animal.errors
            end
    end

Can remove an animal entry in the database ✅

 def destroy
        animal = Animal.find(params[:id])
           if animal.destroy
           render json: animal
           else
               render json: animal.errors
           end
       end

Story 2: In order to track wildlife sightings, as a user of the API, I need to manage animal sightings.

Branch: sighting-crud-actions ✅

Acceptance Criteria

Create a resource for animal sightings with the following information: latitude, longitude, date  ✅

$ rails generate resource Sighting animal_id:integer latitude:string date:date

      invoke  active_record
      create    db/migrate/20220910044504_create_sightings.rb
      create    app/models/sighting.rb
      invoke    rspec
      create      spec/models/sighting_spec.rb
      invoke  controller
      create    app/controllers/sightings_controller.rb
      invoke    erb
      create      app/views/sightings
      invoke    rspec
      create      spec/requests/sightings_spec.rb
      invoke    helper
      create      app/helpers/sightings_helper.rb
      invoke      rspec
      create        spec/helpers/sightings_helper_spec.rb
      invoke  resource_route
       route    resources :sightings
 $ rails db:migrate

Hint: An animal has_many sightings (rails g resource Sighting animal_id:integer ...) 

Hint: Date is written in Active Record as yyyy-mm-dd (“2022-07-28") 

Can create a new animal sighting in the database ✅

 def create 
        animal = Animal.find(params[:sighting][:animal_id]) 
        sighting = animal.sightings.create(sighting_params)
        if sighting.valid?
        render json: sighting
        else
        render json: sighting.errors
        end
    end

    Can update an existing animal sighting in the database✅

      def update
        sighting = Sighting.find(params[:id])
        sighting.update(sighting_params)
        if sighting.valid?
            render json: sighting
        else 
            render json: sighting.errors
        end
    end

Can remove an animal sighting in the database✅

   def destroy
        sighting = Sighting.find(params[:id])
        if sighting.destroy
            render json: sighting
        else 
            render json: sighting.errors
        end
    end

Story 3: In order to see the wildlife sightings, as a user of the API, I need to run reports on animal sightings.

  
Branch: animal-sightings-reports✅

Acceptance Criteria

Can see one animal with all its associated sightings ✅
Hint: Checkout this example on how to include associated records 

class Animal < ApplicationRecord
    has_many:sightings
end

class Sighting < ApplicationRecord
    belongs_to:animal
end

Can see all the all sightings during a given time period ✅

class SightingsController < ApplicationController

def show
        sighting = Sighting.find(id: params[:id])
        if sighting 
        render json: sighting,include:[:animals]
            else 
            render json: {message: 'No sighting found with that id'}
        end

Hint: Your controller can use a range to look like this: class SightingsController < ApplicationController def index sightings = Sighting.where(date: params[:start_date]..params[:end_date]) render json: sightings end end ✅

def create
        sighting = Sighting.create(sighting_params)
        if sighting.valid?
            render json: sighting
            else
                render json: animal.errors
            end
        end

Hint: Be sure to add the start_date and end_date to what is permitted in your strong parameters method ✅

private
    def sighting_params
        params.require(:sighting).permit(:animal_id, :latitude, :longitude, :date, :start_date, :end_date)
    end
end

Hint: Utilize the params section in Postman to ease the developer experience Hint: Routes with params Stretch Challenges 

Story 4: In order to see the wildlife sightings contain valid data, as a user of the API, I need to include proper specs.

Branch: animal-sightings-specs

Acceptance Criteria Validations will require specs in spec/models and the controller methods will require specs in spec/requests.

Can see validation errors if an animal doesn't include a common name and scientific binomial Can see validation errors if a sighting doesn't include latitude, longitude, or a date 

Can see a validation error if an animal's common name exactly matches the scientific binomial 

Can see a validation error if the animal's common name and scientific binomial are not unique 

Can see a status code of 422 when a post request can not be completed because of validation errors

 Hint: Handling Errors in an API Application the Rails Way 
 
 Story 5: In order to increase efficiency, as a user of the API, I need to add an animal and a sighting at the same time.

Branch: submit-animal-with-sightings

Acceptance Criteria

Can create new animal along with sighting data in a single API request Hint: Look into accepts_nested_attributes_for

