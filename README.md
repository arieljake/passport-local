# passport-local

[![Build](https://travis-ci.org/arieljake/passport-local.png)](https://travis-ci.org/arieljake/passport-local)
[![Coverage](https://coveralls.io/repos/arieljake/passport-local/badge.png)](https://coveralls.io/r/arieljake/passport-local)
[![Quality](https://codeclimate.com/github/arieljake/passport-local.png)](https://codeclimate.com/github/arieljake/passport-local)
[![Dependencies](https://david-dm.org/arieljake/passport-local.png)](https://david-dm.org/arieljake/passport-local)

[Passport](http://passportjs.org/) strategy for authenticating with a username
and password.

This module extends the basic LocalStrategy to implement the register function used
by (arieljake/passport).

## Usage

#### Configure Strategy

The local authentication strategy authenticates & registers users using a username and
password.  The strategy requires a `verify` callback, and optionally accepts a `register`
callback which accepts these credentials and calls `done` providing a user.

    passport.use(new LocalStrategy(
      
	  // verify callback
	  function(username, password, done) {
        User.findOne({ username: username }, function (err, user) {
          if (err) { return done(err); }
          if (!user) { return done(null, false); }
          if (!user.verifyPassword(password)) { return done(null, false); }
          return done(null, user);
        });
      },
	  
	  // optional register callback
	  function(username, password, done) {
        User.insert({ username: username, password: password }, function (err, user) {
          if (err) { return done(err); }
          if (!user) { return done(null, false); }
          return done(null, user);
        });
      }
    ));

#### Registration Requests

Use `passport.register()`, specifying the `'local'` strategy, to
register users.

For example, as route middleware in an [Express](http://expressjs.com/)
application:

    app.post('/register', 
      passport.register('local', { failureRedirect: '/register' }),
      function(req, res) {
        res.redirect('/');
      });

## Examples

	passport.use('local', new LocalStrategy(

		// Authenticate Function
		function authenticate(username, password, done)
		{
			mongoDB.collection("users").findOne(
			{
				username: username
			}, 

			function(err, user)
			{
				if (err)
				{
					return done(err);
				}

				if (!user)
				{
					return done(null, false,
					{
						message: 'Incorrect username or password.'
					});
				}

				validatePassword(user, password, function(err, isMatch)
				{
					if (err)
					{
						return done(err);
					}

					if (!isMatch)
					{
						return done(null, false,
						{
							message: 'Incorrect username or password.'
						});
					}

					return done(null, user);
				});
			});
		},

		// Register Function
		function register(username, password, done)
		{
			mongoDB.collection("users").findOne(
			{
				username: username
			}, 
			
			function(err, user)
			{
				if (err)
				{
					return done(err);
				}

				if (user)
				{
					return done(null, false,
					{
						message: 'Username unavailable.'
					});
				}

				// your own generateUser function to create the object you want
				generateUser(username, password, function(err, user)
				{
					if (err)
					{
						return done(err);
					}

					if (!user)
					{
						return done("Failed to process registration.");
					}

					mongoDB.collection("users").insert(
						user,
						function (err, savedUsers)
						{
							if (err)
							{
								return done(err);
							}
							
							if (savedUsers.length != 1)
							{
								return done("Failed to process registration");
							}

							return done(null, savedUsers[0]);
						}
					);
				});
			});
		}
	));

## Tests

    $ npm install
    $ npm test

## Credits

  - [Jared Hanson](http://github.com/jaredhanson)
  - [Ariel Jakobovits](http://github.com/arieljake)

## License

[The MIT License](http://opensource.org/licenses/MIT)
