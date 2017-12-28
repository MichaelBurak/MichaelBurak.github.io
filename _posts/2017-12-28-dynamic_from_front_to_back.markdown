---
layout: post
title:      "Dynamic, From Front To Back"
date:       2017-12-28 21:37:19 +0000
permalink:  dynamic_from_front_to_back
---


Transforming an already existing, static web application into a dynamic application is a rather edifying experience. The process of seeing, say, a page that formerly required a full reload of the page just to get some more information instead loading dynamically and swiftly, when implemented, is joyful coding. Adding jQuery and a JSON API to my existing Ruby on Rails application based around creating and developing role playing game characters was just such an experience. 

The core changes that were made were as follows: 

1. The addition of javascript, specifically jQuery and JSON, to make for a smoother user experience. 
2. Changes in the rails backend to accommodate the use of these frameworks. 
3. Changes in the HTML/ERB of the pages that would be displayed. 

I didn’t really have an exact plan beyond what the specifications of the project were on what order of operations to use to tackle this transition, so I will just briefly give examples of what my process was for key features. 

One thing that was implemented early was on the show page of an individual character, the webpage that would display information about a character. I decided to allow users to sift through the database’s characters using next and back buttons. This was accomplished by first setting up a JSON serializer using the gem Active Model Serializers, transforming data into JSON, and then telling Rails’ Controller for Characters that when showing a character’s JSON, to show the appropriate data in that format. 

(serializers/character_serializer.rb)

class CharacterSerializer < ActiveModel::Serializer
  attributes :id, :name, :subtle, :powerful, :resistant, :level, :xp, :spendable_points, :updated_attributes
  belongs_to :user
  has_one :planner
end

(controllers/characters_controller.rb)

def show
    respond_to do |format|
      format.html {render :show}
      format.json {render json: @character}
    end
  end

(views/characters/show.html.erb)
<p id="notice"><%= notice %></p>


<div class= "js-character">
</div>
<button class= "js-next" data-id="<%= @character.id%>" > Next character</button>
<button class= "js-previous" data-id="<%= @character.id%>"> Previous character </button>

(app/javascripts/characters.js)
//When js-next class button is clicked, set const to button-id + 1 or -1, grab and replace content with
  //next or previous character. Reset button-ids on previous and next buttons.
      $(document).on("click", ".js-next", function() {
        const nextID = parseInt($(".js-next").attr("data-button-id")) + 1;
        $.get("/characters/" + nextID + ".json", function(data) {
          $("#content").html(
          `<p> Character Name: ${data["name"]} </p>
           <p> Subtle: ${data["subtle"]}</p>
           <p> Powerful: ${data["powerful"]} </p>
           <p> Resistant: ${data["resistant"]}</p>
           <p> Level: ${data["level"]}</p>
           <p> XP: ${data["xp"]}</p>
           <p> Created by: ${data["user"]["email"]}</p>
           <p> <button class= "js-next" data-button-id = "${id}"> Next Character </button>
           <p> <button class= "js-previous" data-button-id = "${id}"> Previous Character </button>`
          );
          $(".js-next").attr("data-button-id", data["id"])
          $(".js-previous").attr("data-button-id", data["id"]);
        });
      });
      $(document).on("click", ".js-previous", function() {
        const previousID = parseInt($(".js-previous").attr("data-button-id")) - 1;
        $.get("/characters/" + previousID + ".json", function(data) {
          $("#content").html(
          `<p> Character Name: ${data["name"]} </p>
           <p> Subtle: ${data["subtle"]}</p>
           <p> Powerful: ${data["powerful"]} </p>
           <p> Resistant: ${data["resistant"]}</p>
           <p> Level: ${data["level"]}</p>
           <p> XP: ${data["xp"]}</p>
           <p> Created by: ${data["user"]["email"]}</p>
           <p> <button class= "js-next" data-button-id = "${id}"> Next Character </button>
           <p> <button class= "js-previous" data-button-id = "${id}"> Previous Character </button>`
          );
          $(".js-next").attr("data-button-id", data["id"])
          $(".js-previous").attr("data-button-id", data["id"]);
        });
      })
    });

To put it simply, this is teaching Rails to display a special JSON API endpoint with JSON data, then creating jQuery that triggers when the buttons with the appropriate class are clicked to either display the next or previous character in order, while keeping track of what character is currently being displayed in order to correctly display after a second click of the next or previous button. 

This display of HTML through the cooperation of JS and Ruby on Rails, that full stack of front end and back end, was a primary learning experience and joy of the project process, and implemented in different ways, including displaying an index page and a form that would instantiate new data into the database and then render the response without a page refresh. 

The other core technique I utilized and learned about was JS model objects. To use an example, I implemented a ‘more info’ button on the character index page. Upon loading the page, characters would be displayed simply: just a name and the user they were created by. This button would display more relevant attributes from the database. 

To do this, I set up a new route and page for the character controller to interact with that would contain only the relevant information in JSON format, added a button to introspect on to the index page, and implemented the appropriate javascript to dynamically display this information when the appropriate button was clicked by creating and displaying a prototype.

(routes.rb)
get 'characters/:id(.:format)/expand', to: 'characters#expand'

(controllers/characters_controller.rb)
def expand
      render json: @character.to_json(only: [:subtle, :powerful, :resistant, :level, :xp])
    end

(views/characters/index.html.erb)
<tbody>
    <% @characters.each do |character| %>
      <p> Character Name: <%= character.name %> </p>
      <p> Created by: <%= character.user.email %></p>
      <div class= "js-expand" id="<%=character.id%>" >
      </div>
      <p> <button class= "js-more" data-id="<%=character.id%>"> More info </button>

(javascripts/characters.js)
//When button with js-more class is clicked, introspect on button's data-id corresponding to character's id
//and display more info from API route of expand using newly created ECharacter object and prototypal method.
  $(document).ready(function () {
    function ECharacter(subtle, powerful, resistant, level, xp){
      this.subtle = subtle
      this.powerful = powerful
      this.resistant = resistant
      this.level = level
      this.xp = xp
    }
  $(".js-more").on("click", function() {
    let buttonID = $(this).attr("data-id")
    ECharacter.prototype.expand = function(){
      $(`#${buttonID}`).html(
        `<p> Subtle: ${this.subtle}</p>
         <p> Powerful: ${this.powerful} </p>
         <p> Resistant: ${this.resistant}</p>
         <p> Level: ${this.level}</p>
         <p> XP: ${this.xp}</p>`)
       };
    $.get("/characters/" + buttonID + "/expand/", function(data) {
      expandedCharacter = new ECharacter(data["subtle"], data["powerful"], data["resistant"],
      data["level"], data["xp"])
      expandedCharacter.expand()
     });
   })

These core concepts of displaying information by wiring up the front end and back end in different languages to work together are important for full stack web application development, and were wonderful to program and implement - there is nothing like the satisfaction of a dynamic feature, finally debugged and working, upon that first click displaying the right information. 
