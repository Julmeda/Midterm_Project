# Midterm_Project
## React.js and Strapi Authentication Project
This project aims to implement a simple login and authentication system in a React.js application using Strapi plugins. This project will provide a hands-on experience in creating secure, authenticated applications with these two powerful technologies. 
## Video Link Presentation: 
https://drive.google.com/file/d/1oi-B-JUXWCosarohIKvG0SOHHqXtySTZ/view?usp=sharing
## Prerequisites
To begin working on this project, you'll require the installation of the following components on your computer:
```
* Node.js
* React.js
* Strapi
```
## Installation
### 1. Install Node.js <br>
*To install Node.js on your computer, follow these steps:* <br>
- Visit the official Node.js website
- Download the appropriate installer based on your operating system (LTS version is recommended), and run the installer.
- After installation, verify Node.js and npm (Node Package Manager) by checking their version numbers using the commands "node -v" and "npm -v" in the terminal or command prompt.
### 2. Install React.js
*Here's a step-by-step guide to installing React.js:*
- set up a Node.js environment on your computer
- Once Node.js is installed, open your terminal (or command prompt) and redirect it to the desired installation folder for React.js.
- Run the following command to create a new React application using the create-react-app tool (you can replace "my-react-app" with the desired name of your project):
```
npx create-react-app my-react-app
```
- After the project is created, change the working directory to your newly created React app and run the following command to start the development server:
```
cd my-react-app
npm start
```
- Install the required packages:
```
[axios] - npm install axios
[bootstrap] - npm install bootstrap
[react] - npm install react
[react-dom] - npm install react-dom
[react-router-dom] - npm install react-router-dom
[react-scripts] - npm install react-scripts
[react-toastify] - npm install react-toastify
[reactstrap] - npm install reactstrap
```
### 3. Install Strapi
*Here's a step-by-step guide to installing Strapi:*
-  Install Node.js and npm
-  Once Node.js and npm are installed, open your terminal (or command prompt), redirect it to the desired installation folder and run the following command to install Strapi globally on your system:
```
npm install -g strapi
```
- Create a new Strapi project
```
strapi new my-strapi-project
```
- Navigate to the project directory and start the Strapi server:
```
cd my-strapi-project
strapi start
```
- This will launch the Strapi server, and you'll be able to access the Strapi admin panel and API at http://localhost:1337/admin.
- Set up Strapi. Follow the on-screen instructions to complete the setup process. You'll be prompted to create an administrator account and configure your database.
- Congratulations! You have successfully installed Strapi and set up a new Strapi project.
## Implementing the React.js Application
---Creating the Login Form with Validation and store the JWT Token---
```
const initialUser = { password: "", identifier: "" };

const Login = () => {
  const [user, setUser] = useState(initialUser);
  const navigate = useNavigate();

  const handleChange = ({ target }) => {
    const { name, value } = target;
    setUser((currentUser) => ({
      ...currentUser,
      [name]: value,
    }));
  };

  const handleLogin = async () => {
    const url = `http://localhost:1337/api/auth/local`;
    try {
      if (user.identifier && user.password) {
        const { data } = await axios.post(url, user);
        if (data.jwt) {
          storeUser(data);
          toast.success("Logged in successfully!", {
            hideProgressBar: true,
          });
          setUser(initialUser);
          navigate("/");
        }
      }
    } catch (error) {
      toast.error(error.message, {
        hideProgressBar: true,
      });
    }
  };

  return (
    <Row className="login">
      <Col sm="12" md={{ size: 4, offset: 4 }}>
        <div>
          <h2>Login:</h2>
          <FormGroup>
            <Input
              type="email"
              name="identifier"
              value={user.identifier}
              onChange={handleChange}
              placeholder="Enter your email"
            />
          </FormGroup>
          <FormGroup>
            <Input
              type="password"
              name="password"
              value={user.password}
              onChange={handleChange}
              placeholder="Enter password"
            />
          </FormGroup>
          <Button color="primary" onClick={handleLogin}>
            Login
          </Button>
          <h6>
            Click <Link to="/registration">Here</Link> to sign up
          </h6>
        </div>
      </Col>
    </Row>
  );
};

export default Login;
```
export const storeUser = (data) => {
  localStorage.setItem(
    "user",
    JSON.stringify({
      username: data.user.username,
      jwt: data.jwt,
    })
  );
};
```
```
---Create React.js Authenticated Route Protector Middleware---
```
export const Protector = ({ Component }) => {
  const navigate = useNavigate();

  const { jwt } = userData();

  useEffect(() => {
    if (!jwt) {
      navigate("/login");
    }
  }, [navigate, jwt]);

  return <Component />;
};
```
---Implement Logout functionality---
```
const Logout = () => {
  const navigate = useNavigate();

  useEffect(() => {
    localStorage.setItem("user", "");
    navigate("/login");
  }, [navigate]);

  return null;
};

export default Logout;
```
---Create React.js User Registration Form with Sign-up Functionality---
```
import axios from "axios";
import React, { useState } from "react";
import { useNavigate } from "react-router-dom";
import { toast } from "react-toastify";
import { Col, Row, Button, FormGroup, Input } from "reactstrap";

const initialUser = { email: "", password: "", username: "" };
const Registration = () => {
  const [user, setUser] = useState(initialUser);
  const navigate = useNavigate();

  const signUp = async () => {
    try {
      const url = `http://localhost:1337/api/auth/local/register`;
      if (user.username && user.email && user.password) {
        const res = await axios.post(url, user);
        if (!!res) {
          toast.success("Registered successfully!", {
            hideProgressBar: true,
          });
          setUser(initialUser);
          navigate("/login");
        }
      }
    } catch (error) {
      toast.error(error.message, {
        hideProgressBar: true,
      });
    }
  };

  const handleUserChange = ({ target }) => {
    const { name, value } = target;
    setUser((currentUser) => ({
      ...currentUser,
      [name]: value,
    }));
  };

  return (
    <Row className="register">
      <Col sm="12" md={{ size: 4, offset: 4 }}>
        <div>
          <h2>Sign up:</h2>
          <FormGroup>
            <Input
              type="text"
              name="username"
              value={user.username}
              onChange={handleUserChange}
              placeholder="Enter your full name"
            />
          </FormGroup>
          <FormGroup>
            <Input
              type="email"
              name="email"
              value={user.email}
              onChange={handleUserChange}
              placeholder="Enter your email"
            />
          </FormGroup>
          <FormGroup>
            <Input
              type="password"
              name="password"
              value={user.password}
              onChange={handleUserChange}
              placeholder="Enter password"
            />
          </FormGroup>
          <Button color="primary" onClick={signUp}>
            Sign up
          </Button>
        </div>
      </Col>
    </Row>
  );
};

export default Registration;
```
## Usage
Follow these steps to start the React.js App and Strapi App separately:
1. Go to the folders where you installed the React.js App and Strapi App in separate terminal or command prompt windows.
2. In each terminal, type `npm start` to boot up the React.js and Strapi Apps.
3. Wait until both applications have finished booting up. They should automatically open in your web browser. If they don't, manually enter the provided link from the terminal into your browser's URL bar to open the applications.
4. After successful boot-up, you'll be redirected to the LOGIN page.
5. If you don't have an account, click the REGISTER button below the login form.
6. Once you've completed the registration process, you will be redirected back to the LOGIN page.
7. Use your registered email and password to log in. If successful, you'll be redirected to the HOME page.
8. When you are done using the applications, remember to LOGOUT.

By following these steps, you can effectively start and navigate the React.js and Strapi Apps, perform registration and login actions, and access the HOME page before logging out.
