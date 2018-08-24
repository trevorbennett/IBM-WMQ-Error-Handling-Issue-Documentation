# IBM Websphere MQ Error Handling Issue Documentation

When setting up an MQ using the default settings, as specified in many JMS tutorials, including those written by IBM themselves, you may run in to an issue when you attempt to change the SSLCipherSpec property or value (depending on project specification). If a non recognized spec is given, such as a TLS spec, WMQConnection.java will return an assertion Exception, which upon debugging, won't make a whole lot of sense. The actual casue of this issue is due to a validation that occurs much earlier in the flow, that guarentees failure in the event that your spec is invalid.

So what's actually going on behind the scenes? There is an assertion that in the event that you don't have a matching spec, then you are using a default Connection Factory, which is validated by ensuring port and host name are blank. This assertion doesn't take into account a preoprly configred connection factory that happens to have a misconfigured SSL spec. The Exception throw for a bad spec will never be hit in the event that you provide a valid port or host name, and instead you recieve a generic assertion exception, which leads you to believe that your application is failing to connect to the associated queue due to having a port and host name specified, which obviously doesn't make any sense.

### Causes

There are three common causes of this issue, each with tis own fix.
The most common causes are either miskeying the string value, using a TLS spec type for your SSLCipherSpec while using IBM's cipher list, or not being able to unlock your keystore due to password issues.

* using an SSLCipherSpec value for TLS (the value would begin with TLS_ instead of SSL_) without disabling IBM's Cipher list
* miskeying the value of your MQ queue or QueueManager
* issues related to being able to unlock the keystore

### Fixes

If you have arrived at this page, the odds are extremely high that your issue is the first one, and you have triple checked every field to avoid the obvious issues with the last two. In order to fix the SSLCipherSpec issue, you need to set the VM argument  -Dcom.ibm.mq.cfg.useIBMCipherMappings=false when starting your server or running any given test. Additional explanation for why this is the case can be found [here](https://developer.ibm.com/answers/questions/178651/what-tls-ciphersuites-are-supported-when-connectin.html)

If this doesn't fix your issue, ensure that there are no unexpected spaces or mispellings within your keystore path, your keystore password, your queueManager name, your queue name, etc.

You can attempt to open your keystore with your password just to ensure that the password is valid by entering
    keystore -v -list -keystore path/for/your/keystore.jks

### Contributing

If you have any additional questions not answered by the above, please reach out to github@trvorbennett.us, and I may be able to help and document those problems as well. If you have additional details you've worked through, feel free to create a pull request for this project, and I'll add the info to this readme.

### Additional Resources

[IBM Cipher spec](https://www.ibm.com/support/knowledgecenter/en/SSEQTJ_8.5.5/com.ibm.websphere.ihs.doc/ihs/rihs_ciphspec.html)
