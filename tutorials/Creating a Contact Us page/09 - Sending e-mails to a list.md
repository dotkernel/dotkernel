# Sending e-mail to a list

We'll add one more feature in this lesson. We need to notify someone that a user has sent a message. Be it an admin or a list of admins.

We won't add the e-mail sending logic directly in the controller or service class. We want to do this in a more decoupled way. And by doing so we will also cover more of Zend Framework and DotKernel.

More exactly what we'll cover in this lesson are the mail services provided by our [dot-mail](https://github.com/dotkernel/dot-mail) package and listening to events triggered by the mappers.

We'll cover how to trigger your own events in another lesson. For our purposes we will rely on already defined events that are triggered by any mapper when doing its operations. 

We'll use one of the mapper's save events in order to send a notification e-mail to a predefined list of e-mail addresses. Events are great to completely decouple code. They also provide an extension point and code can be added without changing the original code. Also the decoupling is good because the code that saves the user message to the database should not know about what happens on such an event. You can add anything, from logging to email notifications or additional custom code that should run before or after a particular operation.
