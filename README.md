# UnixPasswordCracker

## Introduction:

This is a 20 year old unix password cracker. It uses crypt module of python to crack the passwords.
It can also crack passwords from shadow file of your linux machine which is located in /etc/shadow.

## Prerequisites

You should have a basic knowledge of hashing, cryptography and python
### understanding the components of a shadow file:

This is a sample shadow file text
```dedsec:$6$9PUOn12d$NdIVCV32guKuwx/habJ2DokF1.jUWBcV5U8OPI3v0PVIjoyFAIEc95mKvINhkFiz3O.Wsoxw97UjX8SEzB6240:17337:0:99999:7:::``` 

Let's now dive into the components of this encrypted txt
* First part (username): the first part is: ```dedsec```, this is defines your username.

* Second Part (hash): This is ```$6$``` in the text. This basically defines which algorithm is being used for encrypting password
  * $1 = MD5 hashing algorithm.
  * $2 = Blowfish hashing algorithm.
  * $2a = eksblowfish hashing algorithm.
  * $5 = SHA-256 hashing algorithm.
  * $6 = SHA-512 hashing algorithm.

Modern unix-based machines use $6 i.e SHA-512 algorithms

![Image of Hashing](https://www.ssl2buy.com/wiki/wp-content/uploads/2015/12/hashing-vs-encryption.png)

The above picture should give you an idea how hashing works and how is it different from encryption and decryption.
If you have some difficulty understanding this then you can always google your problem. I am sure you will get a solution.

* Third Part (salt): This is very very important! This a 8 byte long text which is used to prevent cracking of passwords using rainbow tables. In the text salt is : ```9PUOn12d```

* Fourth Part (password): Now comes the main part, the PASSWORD!!. password in the text is : ```NdIVCV32guKuwx/habJ2DokF1.jUWBcV5U8OPI3v0PVIjoyFAIEc95mKvINhkFiz3O.Wsoxw97UjX8SEzB6240```

Other fields are not required..

For more information about the shadow file visit [This Site](http://www.yourownlinux.com/2015/08/etc-shadow-file-format-in-linux-explained.html).

## Understanding the code

### Modules used:
* ```optparse.OptionParser``` - Used for performing and accepting command line operations
* ```termcolor.colored``` - This is just used to print colored text
* ```crypt``` - main module of this program. Used for encrypting text using specified algorithm

### understanding def main() function:
```python
def main():
  parser = OptionParser()
  parser.add_option("-f",dest = "pname",type = "string",help = "specify password file")
  parser.add_option("-d",dest = "dname",type = "string",help = "specify dictionary file")
  parser.add_option("-u",dest = "user",type = "string",help = "specify username of the victim")
  (options, args) = parser.parse_args()
```

This is the formal method for accepting input using the command line or terminal.

The first line i.e parser = ```OptionParser()```, tells python that this is not a normal variable but a OptionParser object.

Second line adds options to this OptionParser object i.e our parser variable. This actually tells python that whenever user enters something like ```-f somepasswordfile.txt```, It has to be accepted as a string and assigned to pname. ```help``` is not necessary.

Third line adds options for dictionary file and fourth line for username. Everything as a string and assigned to ```dname``` and ```user``` respectively

The fifth line is just like a compiler. It compiles everything that we have told to the ```parser``` object.

Very good, let's move on.

```python
if (options.pname == None) or (options.dname == None) or (options.user == None):
  print(colored("Please specify password file, dictionary file and username","red") + "\n" + colored("example: python3 main.py -f <passwordFile> -d <dictionaryFile> -u <username>","yellow"))
  exit(0)
else:
  pname = options.pname
  dname = options.dname
  user = options.user
```

This is very easy, you might be getting an idea what is that.

This code snippet checks if user enters insufficient aruguments then instead of returning a complex error by python, it prints the information on how to enter the required arguments and then exits the code

Now if the user does enter all the required arguments then simply store each one of them in a variable

Great, let's move on

``` python
passFile = open(pname)
userLen = len(user)
print("[*] Searching for the username "+"'"+user+"' ....")
```
You must be knowing what is this if you have a good amount of knowledge of python. There is nothing special in this

This just opens the password file in the first line and then in the ```userLen``` variable it stores length of the username and then prints the above statement to address the user what the program is doing.

Nothing Special, Let's move on.

``` python
for line in passFile.readlines():
  p = line.strip('\n').strip()
  if p[0:userLen] == user:
    print(colored("[+] Success user found","green"))
    print("[*] Attempting to crack password....")
    p = p[userLen+1:p.index("/")+1]
    cracker(p,dname)
``` 
Now this looks something special.

The ```for``` statements simply readlines from the ```passFile``` in which the password file resides.

Then we strip ```\n``` from the line and store it in variable ```p```

Now, recall our shadow file.

which was this: ```dedsec:$6$9PUOn12d$NdIVCV32guKuwx/habJ2DokF1.jUWBcV5U8OPI3v0PVIjoyFAIEc95mKvINhkFiz3O.Wsoxw97UjX8SEzB6240:17337:0:99999:7:::```

Now the objective of our program for now is to find the username dedsec in the shadow file.

If you see ```userLen``` will be equal to 6 because length of ```dedsec``` is 6.

Now in the first 6 letters of every line of the shadow file we check if that is equal to our username i.e ```dedsec```
If that is true then we execute the code written after it, else we move on to the next line of the shadow file

After we have got the username we now attempt to crack the password. But for cracking the password we should know what is the password. I have explained about under the prerequisites section the various entries of the shadow file. 

```python
p = p[userLen+1:p.index("/")+1]
```
This line extracts the password (ofcourse in the hashed form) from the shadow file. You can do it yourself by messing a little bit with the indices of the shadow file.

```Hint: password starts from $6 just after the ':' which is just after the username and ends at the next ':' ```

But note we only take password tile the index of '/'. That is because there are many ':' in the shadow file.

And after that we call our ```cracker``` function which we will understand after this

Congratulation!! You understood the ```def main()```. Let's move on to our ```cracker``` function.
