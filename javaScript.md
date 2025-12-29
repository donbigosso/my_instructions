# JS Useful code #
# Declaring module JS script #
Place the below (adjust your file) in the bottom of the HTML file (after <.body>)
```
<script type="module" src="./app.js"></script> 
```
All functions executed there will be run. Example:
```
import { onClick } from "./functions/EventFunctions.js";
import { showLoggedOnly,hideLoggedOnly, addRowToTable, deleteRow, hideUnloggedOnly, showUnloggedOnly  } from "./functions/CustomFunctions.js";

const loginButton = document.querySelector("#login-btn");

onClick(loginButton, () => {
  console.log("Login button clicked!");
  showLoggedOnly();
  hideUnloggedOnly();
  });

```
