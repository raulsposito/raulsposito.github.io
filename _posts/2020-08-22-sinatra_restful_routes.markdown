---
layout: post
title:      "Sinatra RESTful Routes"
date:       2020-08-22 20:56:00 -0400
permalink:  sinatra_restful_routes
---


Once again, it's time to create something from scratch. Only this time I was armed with a very diverse arsenal, since it's no longer just Ruby and it's no longer just YOUR code. What makes it interesting is to start thinking really big, to begin working together with others to achieve beyond your personal reach. Every time I used an ActiveRecords macro to define a relationship, the Conreal Gem or Bcrypt to validate a password and handle security I was using pre written code libraries. In order to do that we must rely on conventions to be the middle point between our code and others. Like Frank said: "I want to be a part of it"


The Sinatra Assessment introduced me to many new concepts like DSL, MVC, CRUD and REST. In this article I would like to go over how I handled RESTful Routes for the InnerCleaner Sinatra App.

What I made for Sinatra it’s an App to report the housekeeping jobs done around the house. You can take a picture and post the exact time you clean up something, then you can get reports on who does the most and how many clean up jobs have been done, and so on. This is how the RESTful routes where handled:




| HTTP VERB | ROUTE | ACTION | Used for |
| -------- | -------- | -------- | -------- | -------- |
| GET     | '/jobs' | Index action     | Index page to display all jobs |
| GET     | '/jobs/new'   | New action   | Displays create a job form if the user is logged in. Otherwise redirects to login.      |
| POST | '/jobs‘    | Create action   | Creates one article. Prompts used with message and redirects if successful. Otherwise redirects back and prompts with a message specifying what's missing. |
| GET     | '/jobs/:id'    | Show action  | Displays one job based on ID in the url |
| GET     | '/jobs/:id/edit'    | Edit action   | Displays edit form based on ID in the url only if the user is authorized (owns) that job. |
| PATCH     | '/jobs/:id'    | Update action   | Modifies an existing job based on ID in the url. Then redirects to the job that was modified. |
| DELETE     | ‘/jobs/:id’  | Delete action | Deletes one job based on ID in the url. Then redirects back to jobs. |



As you can see above the REST convention helps knowing what to expect when creating a route to render or a view to display it to the user. It helps us keep organized and almost eliminate the need to comment on what each route is doing. 

It makes it easier for you and the REST ;)


## The Routes

#### Index Action 

```
    # READ 
    get '/jobs' do 
        @jobs = Job.all 
        erb :'jobs/index'
    end
```

#### New Action

```
    # CREATE

    get '/jobs/new' do 
        if logged_in?
            erb :"/jobs/new"
        else
            flash[:error] = "You must be logged in to create a Job!"
            redirect "/"
        end
        
    end

    post '/jobs' do
        job = current_user.jobs.build(title: params[:title], image_url: params[:image_url], description: params[:description], created_at: params[:created_at])
        if job.save
            flash[:message] = "Job Created Successfully!"
            redirect to "/jobs/#{job.id}"
        else
            flash[:error] = "Sorry! Column(s): #{job.errors.full_messages.to_sentence}."
            redirect to "/jobs/new"
        end
    end
```
		
#### Show Action

```
    # READ 
    get '/jobs' do 
        @jobs = Job.all 
        erb :'jobs/index'
    end

    get '/jobs/:id' do 
        @jobs = Job.find(params[:id])
        erb :"/jobs/show"
    end
```

#### Edit Action

```
    # UPDATE 

    get '/jobs/:id/edit' do
        @job = Job.find(params[:id])
        if authorized_user?(@job)
            erb :'/jobs/edit'
        else 
            flash[:error] = "Sorry! You're not authorized to edit that Job."
            redirect "/jobs"
        end
    end

    patch '/jobs/:id' do 
        @job = Job.find(params[:id])
        @job.update(title: params[:title], image_url: params[:image_url], description: params[:description])
        redirect "/jobs/#{@job.id}"
    end
 ```
 
 #### Delete Action
 
 ```
     # DELETE 

    delete '/jobs/:id' do 
        @job = Job.find(params[:id])
        @job.destroy 
        redirect '/jobs'
    end
```

[Check out the repo here: InnerCleanerApp](https://github.com/raulsposito/inner_cleaner_sinatra_app)
		
		
		
		
