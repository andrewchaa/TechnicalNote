##Moq

### Using call back to test values passed

```csharp

static UserRegistrationHandler _handler;
static Registration _registration;
static Mock<IProcessMessages> _commandProcessor;
static CreateIdentityCommand _commandSent;

Establish context = () =>
{
    const string username = "bobo";
    _registration = new Registration(username);

    _commandProcessor = new Mock<IProcessMessages>();
    _commandProcessor.Setup(p => p.Send(Moq.It.IsAny<CreateIdentityCommand>())).Callback<CreateIdentityCommand>(cmd => _commandSent = cmd);

    _handler = new UserRegistrationHandler(_commandProcessor.Object);
};

Because a_registration_is_posted = () => _handler.Post(_registration);

It should_send_the_correct_type_of_command = () => _commandSent.ShouldNotBeNull();
It should_set_the_expected_user_identifier = () => _commandSent.UserIdentifier.ShouldEqual(_registration.UserIdentifier);


```