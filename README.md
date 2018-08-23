# IBM Websphere MQ Error Handling Issue Documentation

When setting up an MQ using the default settings, as specified in many JMS tutorials, including those written by IBM themselves, you may run in to an issue when you attempt to change the SSLCipherSpec property or value (depending on project specification). If a non recognized spec is given, such as a TLS spec, WMQConnection.java will return an assertion Exception, which upon debugging, won't make a whole lot of sense. The actual casue of this issue is due to a validation that occurs much earlier in the flow, that guarentees failure in the event that your spec is invalid.

So what's actually going on behind the scenes? There is an assertion that in the event that you don't have a matching spec, then you are using a default Connection Factory, which is validated by ensuring port and host name are blank. This assertion doesn't take into account a preoprly configred connection factory that happens to have a misconfigured SSL spec. The Exception throw for a bad spec will never be hit in the event that you provide a valid port or host name, and instead you recieve a generic assertion exception, which leads you to believe that your application is failing to connect to the associated queue due to having a port and host name specified, which obviously doesn't make any sense.

### Causes

The most common causes are either miskeying the string value, using a TLS spec type for your SSLCipherSpec while using IBM's cipher list, or not being able to unlock your keystore due to password issues.

### Fixing

The fix for this issue is simply to ensure that you have an SSLCipherSpec value that matches one of the accepted values within your connection factory. This list should be static, and will almost certainly match [IBM's published list which can be found here](https://www-01.ibm.com/software/webservers/httpservers/doc/v10/ibm/9acdciph.htm).

### Resolutions

If you have any additional questions not answered by the above, please reach out to github@trvorbennett.us, and I may be able to help and document those problems as well. If you have additional details you've worked through, feel free to create a pull request for this project, and I'll add the info to this readme.
