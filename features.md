
# Features

- Posting text (formattable), images, videos, albums
- Events that can have people invited, RSVP, etc
- No searchable users; People must find each other through "friend code" style system (this means we can keep people's data completely private until they say that someone can see it)
- Ability to control who can see specific posts
- Profile should be small. We don't want our users' data
	- Name (purely user entry with no validation, let people call themselves whatever they want)
	- Profile Image
- User to user chat
- Ability to create groups of users to keep track of different people or assign view permissions to different people (eg. "Friends", "Family", "People from work")
- Pubilc developer API
	- Security will be tricky; users need to give permissions to an app to access their data.
		- Apps have to be registered with us, and will receive an API key
		- Users have to add the app (by API key), and the app will then receive a new key that it can use to authenticate as that user with the permissions the user grants it
		- Users can revoke a key, or force a key to be regenerated at any time
