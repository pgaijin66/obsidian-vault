
```
import { initializeApp } from "firebase/app";

import { getAuth, GoogleAuthProvider, signInWithPopup } from "firebase/auth";

  

const firebaseConfig = {

apiKey: "AIzaSyBIZvy5XN_yVSEWiCgfuvhf90P0p5SCIXU",

authDomain: "geniee-svc-web.firebaseapp.com",

projectId: "geniee-svc-web",

storageBucket: "geniee-svc-web.appspot.com",

messagingSenderId: "953957904911",

appId: "1:953957904911:web:974f39f8fb90be0921297a"

};

  

// Initialize Firebase

const app = initializeApp(firebaseConfig);

  

const auth = getAuth(app)

  

const provider = new GoogleAuthProvider()

  

const signInWithGoogle = () => {

signInWithPopup(auth, provider)

.then((result) => {

console.log(result)

const name = result.user.displayName!

const email = result.user.email!

const photoURL = result.user.photoURL!

  

localStorage.setItem("name", name)

localStorage.setItem("email", email)

localStorage.setItem("photoURL", photoURL)

  

}).catch((error) => {

console.log(error);

})

}

  
  

const logoutOperation = () => {

auth.signOut()

localStorage.removeItem('name')

localStorage.removeItem('email')

localStorage.removeItem('photoURL')

}

  

export {

signInWithGoogle,

logoutOperation

}
```


```
import {signInWithGoogle, logoutOperation } from "./Firebase"

  

function App() {

  

return (

<div className="App">

<button onClick={signInWithGoogle}>Sign in with google</button>

<h2>{localStorage.getItem("name")}</h2>

<h2>{localStorage.getItem("email")}</h2>

<img src={localStorage.getItem("photoURL")} alt="" />

<button onClick={logoutOperation}>Logout</button>

</div>

)

}
```