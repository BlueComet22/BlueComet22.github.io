<html>
  <head>
    <title>Example Show Controls</title>
    <link rel="stylesheet" type="text/css" href="main.css">
    <script type="text/javascript" src="openspace-api.js"></script>
    <script type="text/javascript" src="openspace-default-buttons.js"></script>

    <script type="text/javascript">
      //variable for js libarary
      var openspace = null;

      const delay = ms => new Promise(res => setTimeout(res, ms));

      var htmlFunction = (action, id) => {
          var cardHTML = "<div id=\"" + action.title + "\" class='card'><h2>" + action.title + "</h2>";
          if (action.description) {
            action.description.split('\n').map(item => {
              cardHTML += "<p>" + item + "</p>";
            });
          }
          if (action.buttons) {
            Object.keys(action.buttons).map(button => {
              const fn = action.buttons[button];
              cardHTML += "<button id=\"" + action.title+button + "\">"+ button +"</button>";
            });
          }
          cardHTML += "</div>";
          document.getElementById('main').innerHTML += cardHTML;
      };

      var clickFunction = (action, id) => {
        if (action.buttons) {
          Object.keys(action.buttons).map(button => {
              const fn = action.buttons[button];
              document.getElementById(action.title+button).addEventListener("click", fn);
          });
        }
      }

      //helper function to map the buttons to html
      function mapButtons(openspace) {
        document.getElementById('main').innerHTML += "<div class='card'>Default Buttons</div>"
        defaultButtonGroups.map(htmlFunction);
        defaultButtonGroups.map(clickFunction);
      }

      //helper function to connect to opensapce
      var connectToOpenSpace = () => {
        //setup the api params
        var host = document.getElementById('ipaddress').value;
        var api = window.openspaceApi(host, 4682);
        //notify users on disconnect
        api.onDisconnect(() => {
          console.log("disconnected");
          document.getElementById('container').className = "disconnected";
          document.getElementById('connection-status').style.opacity = 1;
          var disconnectedString = "Connect to OpenSpace: ";
          disconnectedString += '<input id="ipaddress" type=text placeholder="Enter ip address" /> ';
          disconnectedString += '<button onClick="connectToOpenSpace();">Connect</button>';
          document.getElementById('connection-status').innerHTML = disconnectedString;
          openspace = null;
        });
        //notify users and map buttons when connected
        api.onConnect(async () => {
          try {
            document.getElementById('container').className = "connected";
            document.getElementById('connection-status').innerHTML = "";
            openspace = await api.library();
            console.log('connected');
            // mapButtons(openspace);
          } catch (e) {
            console.log('OpenSpace library could not be loaded: Error: \n', e);
            return;
          }
        })
        //connect
        api.connect();
      };

      function logMessage(message) {
        console.log(message);
        document.getElementById('connection-status').innerHTML = message;
        document.getElementById("connection-status").style.opacity = 1;
        fadeOut();
      }

      // function that fades out the log div after 5 seconds
      function fadeOut() {
        setTimeout(function() {
          var fade = document.getElementById("connection-status");
          var timerId = setInterval(function() {
            var opacity = fade.style.opacity;
            if (opacity == 0.0) {
              clearInterval(timerId);
            } else {
              fade.style.opacity = opacity - 0.05;
            }
          }, 100);
        }, 5000);
      }

      async function fadeOutSceneGraphNode(sgn, option) {
        openspace.setPropertyValue('Scene.' + sgn + '.Renderable.Opacity', 0.0, 1.0);
        if (option != 'NoDisable') {
          await delay(1500);
          openspace.setPropertyValue('Scene.' + sgn + '.Renderable.Enabled', false)    ;
        }
      }
      function fadeInSceneGraphNode(sgn) {
        openspace.setPropertyValue('Scene.' + sgn + '.Renderable.Opacity', 0.0);
        openspace.setPropertyValue('Scene.' + sgn + '.Renderable.Enabled', true);
        openspace.setPropertyValue('Scene.' + sgn + '.Renderable.Opacity', 0.9, 1.0);
      }

      function fadeInTagGroup(sgn) {
        openspace.setPropertyValue(sgn + '.Renderable.Opacity', 0.0);
        openspace.setPropertyValue(sgn + '.Renderable.Enabled', true);
        openspace.setPropertyValue(sgn + '.Renderable.Opacity', 0.9, 1.0);
      }

      async function fadeOutTagGroup(sgn, option) {
        openspace.setPropertyValue(sgn + '.Renderable.Opacity', 0.0, 1.0);
        if (option != 'NoDisable') {
          await delay(1500);
          openspace.setPropertyValue(sgn + '.Renderable.Enabled', false)    ;
        }
      }
      async function fadeOutColorLayer(planet, layer) {
        openspace.setPropertyValueSingle('Scene.' + planet + '.Renderable.Layers.ColorLayers.' + layer + '.Settings.Opacity', 0.0, 1.0);
        await delay(1500);
        openspace.setPropertyValueSingle('Scene.' + planet + '.Renderable.Layers.ColorLayers.' + layer + '.Enabled', false);
      }

      function fadeInColorLayer(planet, layer) {
        openspace.setPropertyValueSingle('Scene.' + planet + '.Renderable.Layers.ColorLayers.' + layer + '.Settings.Opacity', 0.0);
        openspace.setPropertyValueSingle('Scene.' + planet + '.Renderable.Layers.ColorLayers.' + layer + '.Enabled', true);
        openspace.setPropertyValueSingle('Scene.' + planet + '.Renderable.Layers.ColorLayers.' + layer + '.Settings.Opacity', 1.0, 1.0);
      }

      async function disableHeightLayer(planet, layer) {
        openspace.setPropertyValueSingle('Scene.' + planet + '.Renderable.Layers.HeightLayers.' + layer + '.Enabled', false);
      }

      function enableHeightLayer(planet, layer) {
        openspace.setPropertyValueSingle('Scene.' + planet + '.Renderable.Layers.HeightLayers.' + layer + '.Enabled', true);
      }

      function flytosomewhere(whereto) {
        openspace.pathnavigation.flyTo(whereto);
        openspace.time.setPause(true)

      }

    </script>


  </head>
  <body>
    <!-- HTML Containers -->
    <div id="container" class="disconnected">
      <div id="connection-status" class="connection-status">
        Connect to OpenSpace:
        <input id='ipaddress' type=text placeholder="Enter ip address"/>
        <button onClick="connectToOpenSpace();">Connect</button>
      </div>

      <div >
        <h2 style="color: #b0eaff">An exploration of the four Galilean moons.</h2>
      </div>
      <div class="script">

        <p>
          Have you ever looked up at the sky and wonder how us humans discovered those celestial bodies
          and who's been doing it all these years? Since before written history, we've studied the stars
          for thousands of years.
        </p>
        <p>Today, we'll be lookinga at the discoveries of Galileo Galilei, who found four huge moons of
        Jupiter from right her on Earth with his telescope.</p>
      </div>

      <div class='card'>
        <h2>Stars</h2>
        <div class="group">
          <span class="groupName">Turn Stars On/Off</span>
        <button onClick="flytosomewhere('Jupiter');">Fly To Jupiter</button>
        </div>
      </div>


      <div class="script">

        <p>
          Let's go to Mars!
        </p>

      </div>

      <div class='card'>
          <div class="group">
        <button onClick="flytosomewhere('Mars');">Fly To Mars</button>
      </div>
      </div>

      <div class="script">

        <p>
          How do we know where we are? Turn on some Grids:
        </p>

      </div>
            <div class='card'>
        <h2>Grids</h2>
        <div class="group">
          <span class="groupName">Equatorial Sphere</span>
          <button onClick="fadeInSceneGraphNode('EquatorialSphere');">On</button>
          <button onClick="fadeOutSceneGraphNode('EquatorialSphere');">Off</button>
        </div>
        <div class="group">
          <span class="groupName">Ecliptic Sphere</span>
          <button onClick="fadeInSceneGraphNode('EclipticSphere');">On</button>
          <button onClick="fadeOutSceneGraphNode('EclipticSphere');">Off</button>
        </div>



      </div>

      <div class="script">

        <p>
          The Constellations helped guide travelers for eons.
        </p>

      </div>



      <div class='card'>
        <h2>Constellations</h2>
        <div class="group">
          <span class="groupName">Constellations</span>
        <button onClick="fadeInSceneGraphNode('Constellations');">On</button>
        <button onClick="fadeOutSceneGraphNode('Constellations');">Off</button>
        </div>
        <div class="group">
          <span class="groupName">Constellations Art</span>
        <button onClick="fadeInSceneGraphNode('ImageConstellation*');">On</button>
        <button onClick="fadeOutSceneGraphNode('ImageConstellation*');">Off</button>
        </div>

      </div>




      </div>

      <div id="main">
      </div>
      <script type="text/javascript">
        connectToOpenSpace('localhost');
      </script>
    </div>
  <body>
</html>
