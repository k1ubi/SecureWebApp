<div align="center">

```
 _____ _____ _____ _   _______ _____   _    _ ___________  ___  ____________ 
/  ___|  ___/  __ \ | | | ___ \  ___| | |  | |  ___| ___ \/ _ \ | ___ \ ___ \
\ `--.| |__ | /  \/ | | | |_/ / |__   | |  | | |__ | |_/ / /_\ \| |_/ / |_/ /
 `--. \  __|| |   | | | |    /|  __|  | |/\| |  __|| ___ \  _  ||  __/|  __/ 
/\__/ / |___| \__/\ |_| | |\ \| |___  \  /\  / |___| |_/ / | | || |   | |    
\____/\____/ \____/\___/\_| \_\____/   \/  \/\____/\____/\_| |_/\_|   \_|    
```

`[ AES session tokens :: salted SHA-256 :: upload sanitation :: HTTPS-enforced ]`

![java](https://img.shields.io/badge/JAVA-ff00c8?style=for-the-badge&logo=openjdk&logoColor=00fff9&labelColor=0a0014)
![mysql](https://img.shields.io/badge/MYSQL-00fff9?style=for-the-badge&logo=mysql&logoColor=0a0014&labelColor=0a0014)
![tomcat](https://img.shields.io/badge/TOMCAT-ff00c8?style=for-the-badge&logo=apachetomcat&logoColor=00fff9&labelColor=0a0014)
![status](https://img.shields.io/badge/STATUS-MASTER'S_THESIS_BUILD-00fff9?style=for-the-badge&labelColor=0a0014)

</div>

<br>

```
▓▒░ 0x00 // SITREP ░▒▓
```

Servlet-based web app built for a Master's degree in Cyber Security — the assignment was to
harden every layer a typical student CRUD app gets wrong: session handling, password storage,
file uploads, transport. Full writeup lives in `Documentazione progetto.pdf`.

<br>

```
▓▒░ 0x01 // DEFENSIVE STACK ░▒▓
```

| layer | what's actually implemented |
|---|---|
| session tokens | `SessionManagement` mints a per-login token from a random string + email fragment, AES-encrypts it (`com.swa.crypt.AES`), and stores it in a cookie — validated server-side on every request |
| password storage | `PasswordHash` salts each password with a random salt (stored per-user in a `salt` table) and hashes with SHA-256; salt bytes are wiped from memory after use |
| file uploads | `ContentExtraction` runs Apache Tika content-sniffing on every upload and regex-scans for injected `<script>` tags before a file is trusted |
| database | every query in `UserDao`/`Database` goes through `PreparedStatement` — no string-built SQL |
| transport | enforced over HTTPS |

<br>

```
▓▒░ 0x02 // MODULE MAP ░▒▓
```

```
src/main/java/com/swa/
├── crypt/            AES.java · PasswordHash.java
├── session/           SessionManagement.java · RandomString.java
├── files/             ContentExtraction.java · *UploadServlet.java · URLBuilder.java
├── user/              Controller.java · Login/Logout/Profile/UserServlet.java · UserDao.java
└── dbconnection/      Database.java
```

Front end is plain JSP (`login.jsp`, `profile.jsp`, `upload.jsp`, `projectUpload.jsp`, …) with
client-side checks in `assets/js/` (`inputValidator.js`, `passwordValidation.js`) backing up the
server-side checks — never trusting the browser alone.

<br>

```
▓▒░ 0x03 // RUN IT ░▒▓
```

```console
root@node:~/SecureWebApp# mysql < TruncateTable.sql        # reset schema
root@node:~/SecureWebApp# # import as an Eclipse Dynamic Web Project (.classpath/.project already set up)
root@node:~/SecureWebApp# # JARs are vendored in src/main/webapp/WEB-INF/lib — export as WAR, deploy to Tomcat
```

<br>

<div align="center">

`GNU GPL` terms not asserted in-repo — treat as coursework reference, ask before reuse.

</div>
