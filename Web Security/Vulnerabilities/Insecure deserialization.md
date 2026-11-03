![[Pasted image 20260505064559.png]]

# What is serialization?
**Serialization** is the process of converting complex data structures, such as objects and their fields, into a "flatter" format that can be sent and received as a sequential stream of bytes. Serializing data makes it much simpler to:
- Write complex data to inter-process memory, a file, or a database
- Send complex data, for example, over a network, between different components of an application, or in an API call

# Deserialization
**Deserialization** is the process of restoring this byte stream to a fully functional replica of the original object, in the exact state as when it was serialized.
![[Pasted image 20260505065413.png]]

## What is insecure deserialization?
Insecure deserialization is when user-controllable data is deserialized by a website. This potentially enables an attacker to manipulate serialized objects in order to pass harmful data into the application code.
It is even possible to replace a serialized object with an object of an entirely different class. Alarmingly, objects of any class that is available to the website will be deserialized and instantiated, regardless of which class was expected. For this reason, insecure deserialization is sometimes known as an "object injection" vulnerability.
An object of an unexpected class might cause an exception. By this time, however, the damage may already be done. Many deserialization-based attacks are completed **before** deserialization is finished. This means that the deserialization process itself can initiate an attack, even if the website's own functionality does not directly interact with the malicious object. For this reason, websites whose logic is based on strongly typed languages can also be vulnerable to these techniques.

### 1. PHP serialization format
PHP uses a mostly human-readable string format, with letters representing the data type and numbers representing the length of each entry. For example, consider a `User` object with the attributes:
`$user->name = "carlos"; $user->isLoggedIn = true;`

When serialized, this object may look something like this:
`O:4:"User":2:{s:4:"name":s:6:"carlos";s:10:"isLoggedIn":b:1;}`

The native methods for PHP serialization are `serialize()` and `unserialize()`. If you have source code access, you should start by looking for `unserialize()` anywhere in the code and investigating further.

### 2. Java serialization format
Some languages, such as Java, use binary serialization formats. This is more difficult to read, but you can still identify serialized data if you know how to recognize a few tell-tale signs. For example, serialized Java objects always begin with the same bytes, which are encoded as `ac ed` in hexadecimal and `rO0` in Base64.

Any class that implements the interface `java.io.Serializable` can be serialized and deserialized. If you have source code access, take note of any code that uses the `readObject()` method, which is used to read and deserialize data from an `InputStream`.

---

*there are two approaches you can take when manipulating serialized objects. You can either edit the object directly in its byte stream form, or you can write a short script in the corresponding language to create and serialize the new object yourself. The latter approach is often easier when working with binary serialization formats.*


#Portswigger 
1. Modifying serialized objects #APPRENTICE 
	change the cookies to this
	`O:4:"User":2:{s:8:"username";s:6:"wiener";s:5:"admin";b:1;}` *PHP serialize*
2. 

## Impact of insecure deserialization
1. RCE
2. privilege escalation
3. arbitrary file access
4. DOS





# Common defenses against deserialization
1. Avoid deserialization

# Notes:
- Be aware that when working with different programming languages, serialization may be referred to as marshalling (Ruby) or pickling (Python). These terms are synonymous with "serialization" in this context.
- Note that all of the original object's attributes are stored in the serialized data stream, including any private fields. To prevent a field from being serialized, it must be explicitly marked as "transient" in the class declaration.