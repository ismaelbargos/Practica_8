Plataphorma

Meteor pet project created to teach my students the following Meteor functionality: 

* Collection's publish/subscribe 
* Deps.autorun 
* Meteor.methods/call 
* Integration of non-Meteor code in compatibility folder (HTML5 games Alien Invasion and Froot Wars)
* Usage of allow to control client access to collections

![ScreenShot](/screenshot.png)


Plataphorma offers the possibility to run 2 different HTML5 games: Alien Invasion and Froot Wars. 

On the right side of the screen the best players of each game are shown, updated in real time each time a signed in player finishes a game. If no game is selected, the best players overall are shown.

On the left side of the screen a chatroom for the current game is available. Only signed in users can post messages. If no game is selected a general chatroom is shown.

The original code of the two HTML5 games integrated in this project is available here:
* Alien Invasion: https://github.com/cykod/AlienInvasion
* Froot Wars: http://www.wrox.com/WileyCDA/WroxTitle/Professional-HTML5-Mobile-Game-Development.productCd-1118301323,descCd-DOWNLOAD.html

Bootstrap style (file bootstrap.min.css) provided by http://bootswatch.com


Running the project
-------------------

A live version of this code is running here: http://plataphorma.meteor.com

To run the project locally, clone the repo and run ```meteor``` inside it. You can see in .meteor/packages that this Meteor project uses these packages:
* ```meteor remove autopublish```
* ```meteor remove insecure```
* ```meteor add bootstrap```
* ```meteor add accounts-ui```
* ```meteor add accounts-password```


Practica_8

 1. El código que se ejecuta es el siguiente:
    
      Template.choose_game.events = {
          'click #AlienInvasion': function () {
	      $('#gamecontainer').hide();
	      $('#container').show();
	      var game = Games.findOne({name:"AlienInvasion"});
	      Session.set("current_game", game._id);
          },
          'click #FrootWars': function () {
	      $('#container').hide();
	      $('#gamecontainer').show();
	      var game = Games.findOne({name:"FrootWars"});
	      Session.set("current_game", game._id);
          },
          'click #none': function () {
	      $('#container').hide();
	      $('#gamecontainer').hide();
	      Session.set("current_game", "none");
          }
      }

En este código dependiendo de sobre que juego se haga click se oculta el canvas del juego contrario y se dibuja el canvas del juego sobre el que se ha pinchado. A parte de esto actualiza el valor de current_game con el valor de la pestaña sobre la que se ha clicado.

  2. 3.  El código que se ejecuta en el apartado 2 y 3 es el siguiente:

    Template.input.events = {
      'keydown input#message' : function (event) {
	      if (event.which == 13) { 
	          if (Meteor.userId()){
		          var user_id = Meteor.user()._id;	    
		          var message = $('#message');
		          if (message.value != '') {
		              Messages.insert({
			          user_id: user_id,
			          message: message.val(),
			          time: Date.now(),
			          game_id: Session.get("current_game")
		              });
		            message.val('')
		          }
	          }
	          else {
		          $("#login-error").show();
	          }
	      }
      }
    }

  
Para el apartado 2:
  Entramos por el if de event.which == 13 pero en la siguiente línea como no estamos registrados la función de meteor "Meteor.userId()" nos devolvera false por lo tanto iremos por la rama del else y mostraremos un mensaje de login error.

Para el apartado 3:
  Entramos por el if de event.which == 13 la siguiente línea, "Meteor.userId()" se pondra a true ya que somos un usuario logueado por lo tanto obtendremos nuestra id de usuario con "Meteor.user()._id", obtendremos el mensaje a traves de "$('#message') y ahora si el mensaje es distinto de vaciolo insertaremos pasandole estos dos parametros y la hora actual, y el juego en el que nos encontramos actualmente.

  4. 5. Para el apartado 4 y 5 el codigo que se ejecuta es el siguiente que se encuentra en server.js:

    Meteor.methods({
      matchFinish: function (game, points) {
	    // Don't insert in the Matches collection a match if the user
	    // has not signed in
	    if (this.userId)
	        Matches.insert ({user_id: this.userId, 
			         time_end: Date.now(),
			         points: points,
			         game_id: game
			        });
        }
    });

Apartado 4
  Cuando se termina la partida, ya sea porque se ha superado los niveles o porque nos han "matado", se ejecuta la función "matchFinish" donde podemos observar que si no estamos logueados no hara nada ya que (this.userId) estara vacio por lo tanto sera false.

Apartado 5
  Cuando se termina la partida, ya sea porque se ha superado los niveles o porque nos han "matado", se ejecuta la función "matchFinish" donde podemos observar que si no estamos logueados no hara nada ya que (this.userId) estara vacio por lo tanto sera true y no ejecutaremos el insert que se encuentra dentro del if el cual inserta en Matches el usuario, la hora de la finalizacion de la partida, los puntos y el juego en el que ha conseguido la máxima puntuación.


  6. 7. Para los apartados 6 y 7 el codigo que se ejecuta es el siguiente:

    var currentUser = null;
    Deps.autorun(function(){
        console.log("current user: " + currentUser);
        currentUser = Meteor.userId();
        console.log("current user: " + currentUser);
    });

Apartado 6
  Cuando el usuario no esta logueado, se pasa dos parametros current user, el primero "null" al no estar logueado y el segundo el que devuelve la función Meteor.userId().

Apartado 7
  Sucede al reves que sucede en el apartado 6. Al estar logueado se le pasa el id del usuario y posteriormente currentUser = null.

