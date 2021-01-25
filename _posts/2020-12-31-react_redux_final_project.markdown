---
layout: post
title:      "React/Redux Final Project"
date:       2020-12-31 19:16:43 -0500
permalink:  react_redux_final_project
---


![](https://media.giphy.com/media/hqaE7SxSbf0CogR1I2/giphy.gif)

We made it! It's time to present my final project. And "final project" was never further from truth, because this is hardly my final project, this is a new beggining and for many more projects to come.

As I race towards the finish line, this project's idea mutated a few times, let me try to go over the process with you...

This story starts with a deadline, Dec. 31st. Last day of the year is also my last day to submit the project. ( what are the chances of this? ) So, I had to rush into thinking an idea and I came up with Quote-Market. The idea was to have a market place app for people to post jobs they needed done, under whatever category, and people can apply to those jobs leaving their quotation for it. So if you wanted someone to do the yard or if you need a graphic designer or whatever, you can post it there, and also on the other hand you can browse for jobs and maybe find something you can do.

A few days into the project I find a twist for this idea. As I was watching Cupcake & Dino, I realize I can make it a bit more fun by adding the characters to the app and actually using them as a theme. Now, the job applicant becomes a hero, that would do the job for you on demand. A General Services app.

Ok, so that said... let's dive into the technical aspects.

First the backend. At this point, it feels like home to do "ROR stuff". Creating the backend API felt so natural, familiar. The complicated parts about views were not involved so it all became a matter of "molding" reality throughout the models, their relationships and attributes.

For this project I have:


```
class User < ApplicationRecord
    has_secure_password

    has_many :posts
    has_many :quotes
    
    has_many :markets, through: :quotes

    validates :email, uniqueness: true
    validates :name, :email, :pic_url, presence: true
end

```

```
class Post < ApplicationRecord
    belongs_to :user
    belongs_to :market
    has_many :quotes
end

```

```
class Market < ApplicationRecord
    has_many :posts
    has_many :quotes 
    has_many :users, through: :quotes
end

```

```
class Quote < ApplicationRecord
    belongs_to :post
    belongs_to :user
    belongs_to :market 
end

```

After migrating the schema file looks like this:

```
# This file is auto-generated from the current state of the database. Instead
# of editing this file, please use the migrations feature of Active Record to
# incrementally modify your database, and then regenerate this schema definition.
#
# This file is the source Rails uses to define your schema when running `rails
# db:schema:load`. When creating a new database, `rails db:schema:load` tends to
# be faster and is potentially less error prone than running all of your
# migrations from scratch. Old migrations may fail to apply correctly if those
# migrations use external dependencies or application code.
#
# It's strongly recommended that you check this file into your version control system.

ActiveRecord::Schema.define(version: 2021_01_06_150534) do

  # These are extensions that must be enabled in order to support this database
  enable_extension "plpgsql"

  create_table "markets", force: :cascade do |t|
    t.string "name"
    t.datetime "created_at", precision: 6, null: false
    t.datetime "updated_at", precision: 6, null: false
  end

  create_table "posts", force: :cascade do |t|
    t.string "picture"
    t.string "description"
    t.integer "budget"
    t.integer "user_id"
    t.datetime "created_at", precision: 6, null: false
    t.datetime "updated_at", precision: 6, null: false
    t.integer "market_id"
  end

  create_table "quotes", force: :cascade do |t|
    t.float "price"
    t.string "comment"
    t.integer "post_id"
    t.integer "user_id"
    t.datetime "created_at", precision: 6, null: false
    t.datetime "updated_at", precision: 6, null: false
    t.integer "market_id"
  end

  create_table "users", force: :cascade do |t|
    t.string "name"
    t.string "email"
    t.string "pic_url"
    t.string "password_digest"
    t.datetime "created_at", precision: 6, null: false
    t.datetime "updated_at", precision: 6, null: false
  end

end

```

Also as you can see, I used Posgresql for the database.

The idea was to have some CRUD functionality for the resources so I had the corresponding routes for them and also added a few custom ones just to have Sessions functionality:

```

  post "/api/v1/login", to: "api/v1/sessions#create"
  post "/api/v1/signup", to: "api/v1/users#create"
  delete "/api/v1/logout", to: "api/v1/sessions#destroy"
  get "/api/v1/get_current_user", to: "api/v1/sessions#get_current_user"
```

Needed serializers too, so this time I decided to try FastJsonAPI. Easy to use and with good performance. 

```
class UserSerializer
  include FastJsonapi::ObjectSerializer
  attributes :name, :email, :pic_url

end

```

As you can see it is just a simple use of the serializer.

Cool, so like I said, we have Sessions functionality, which means, sessions controller.

```
class Api::V1::SessionsController < ApplicationController
    def create
        #binding.pry
        @user = User.find_by(email: params[:session][:email])

        if @user && @user.authenticate(params[:session][:password])
            session[:user_id] = @user.id
            render json: UserSerializer.new(@user), status: :ok
        else 
            render json: {
                error: "Invalid Credentials"
            }
        end
    end

    def get_current_user
        if logged_in?
            render json: UserSerializer.new(current_user)  # this current_user
        else
            render json: {
                error: "Please Log In to continue."
            }
        end
    end

    def destroy
        session.clear
        render json: {
          notice: "successfully logged out"
        }, status: :ok
    end    

end

```



![](https://media.giphy.com/media/3OyjsGEYml9iazJrDW/giphy.gif)

Enough backend for a bit. I want to show you the fun part. It's called the React/Redux project for a reason.

After you create the new React app, you are handed a bunch of useful things and a cool layout to start.

I started building on top of that and the idea was to have an MVP ready to present on time so the main track would be to either look for a hero (post a job that needs help) or apply to be hero material (browse for jobs that need to be done).

My app at this point has a few routes, it will grow as I add some of my stretch goals for the project but right now it looks like this:

```
import React from 'react';
import './App.css';
import NavBar from "./components/NavBar";
import Header from "./components/Header"
import Home from './components/Home.js'
import Signup from './components/Signup.js'
import Login from './components/Login'
import Logout from './components/Logout'
import PostsContainer from './components/PostContainer'
import UsersContainer from './components/UsersContainer'
import SinglePost from './components/SinglePost'
import NewPostForm from './components/NewPostForm'
import MainContainer from './components/MainContainer'
import Footer from './components/Footer'
import { connect } from 'react-redux'
import { getCurrentUser } from "./actions/currentUser.js"

import { Route, Switch, withRouter } from 'react-router-dom';

class App extends React.Component {

  componentDidMount() {
    this.props.getCurrentUser()
  }
  
  render(){
    const { loggedIn } = this.props
    return (
      <div className="all">
        <Header />
        <div className="App">
          { loggedIn ? <MainContainer /> : "" }
          { loggedIn ? <NavBar /> : <Home /> }
          <Switch>
            <Route exact path='/signup' component={Signup}/>
            <Route exact path='/login' component={Login}/>
            <Route exact path='/posts' component={PostsContainer}/>
            <Route exact path='/posts/new' component={NewPostForm}/>
            <Route exact path='/posts/:id' component={SinglePost}/>
            <Route exact path='/users' />
          </Switch>
          { loggedIn ? <Logout /> : "" }
          <Footer/>
        </div>
      </div>
    );
  }
  
}

const mapStateToProps = state => {
  return {
    loggedIn: !!state.currentUser
  }
}

export default withRouter(connect(mapStateToProps, { getCurrentUser })(App));

```

Upon mounting the component it fires a fetch to see wether someone is logged in, so it can decide which content to deploy. 

The flow of the app is introduced after loggin in with the Navbar component. Here's were you either post or browse for jobs:

```
import React from 'react'
import { connect } from 'react-redux'
import { NavLink } from 'react-router-dom'

const NavBar = () => {
    return (
        <div className="navbar">
            <p><NavLink to="/">Home</NavLink><br></br></p>
            <p><NavLink to="/users">Meet our heroes</NavLink></p>
            <div className="row">
                <div className="column" id="find">
                    <NavLink to="/posts/new">Find a Hero!</NavLink><br></br>
                </div>
                <div className="column" id="be">
                    <NavLink to="/posts">Be a Hero!</NavLink><br></br>
                </div>
            </div>
        </div>
    )
}

const mapStateToProps = ({ currentUser }) => {
    return {
        currentUser
    }
}

export default connect(mapStateToProps)(NavBar)
```


Another nice thing about React is thinking in Atomic design terms. For example, to render all the Posts submitted I first had a PostContainer component render a PostCard component inside of it, as it mapped the entire array of posts.

It goes like this...

```
import React from 'react'
import { connect } from 'react-redux'
import { getPosts } from '../actions/posts'
import PostCard from './PostCard'
import { v4 as uuidv4 } from 'uuid';

class PostContainer extends React.Component {

    componentDidMount() {
        this.props.getPosts()
    }

    render() {
        return(
            <ul className="post-feed">
                { this.props.posts.length > 0 ? this.props.posts.map(post => <li key={uuidv4()}><PostCard post={post}/></li>) : <p>Nope!</p>} 
            </ul>
        )
    }
}

const mapStateToProps = ( state ) => {
    return {
        posts: state.posts
    }
}

export default connect(mapStateToProps, { getPosts })(PostContainer)
```

and PostCard

```
import React from 'react'
import { Link } from 'react-router-dom'
import QuoteForm from './QuoteForm'

const PostCard = ({ post }) => {
    return (
        post ?
        <div className="post-card">
            <div>
                <img className="pic" src={post.picture} alt="post picture" />
            </div>
            <p>{post.description}</p>
            <p>Hourly pay:{post.budget}</p>
            <Link to={`/posts/${post.id}`}>See this post!</Link>
            <QuoteForm />
        </div> :
        <p>No help needed here!</p>
    )
}

export default PostCard
```

That is awesome about React!

Tough part for me was understanding Redux. I really rushed to understand it but it only clicked when I had to face the issue that gave birth to it, State. Or better said, handling State.

Long story short, I felt like it was easier to have all handed to Redux and have one source of truth, accessible from anywhere in the app where needed.

So I adedd everything I could to the Store file:

```
import { createStore, applyMiddleware, compose, combineReducers } from 'redux'
import postsReducer from './reducers/posts.js'
import currentUser from './reducers/currentUser.js'
import loginForm from './reducers/loginForm.js'
import signupForm from './reducers/signupForm'
import newPostForm from './reducers/newPostForm'
import quoteForm from './reducers/quoteForm'
import thunk from 'redux-thunk'

const reducer = combineReducers({
    currentUser,
    loginForm,
    signupForm,
    posts: postsReducer,
    newPostForm,
    quoteForm
})

const composeEnhacer = window.__REDUX_DEVTOOLS_EXTENSION_COMPOSE__ || compose;

const store = createStore(reducer, composeEnhacer(applyMiddleware(thunk)))

export default store
```


There's a lot of magic hidden into that code. After that you're able to access that store and shop for state of any component, pass it into whatever you want to deploy... and it's magic!



![](https://media.giphy.com/media/551JwsfiorBvNc6fOH/giphy.gif)


As I said at the beggining, this is not the final project, it is just one many. I have found a profound love for coding and I can't wait to continue to learn and build new things. Also to revisit and add to this and all the project's I've started at Flatiron. 



