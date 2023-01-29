---
title: "ActivityPub Eats Your Brain!"
date: 2023-01-19T17:43:08Z
description: "Demystifying ActivityPub, one activity at a time."
type: "post"
tags: ["activitypub", "tutorial", "guide", "python", "fediverse"]
---


## What's in a name?

Close your eyes and imagine you're inside a dimly lit, energetic pub filled with unicorns, giant centipedes, and paper airplanes. Actually, please don't close your eyes, since then you won't be able to read this. But anyways, you're completely puzzled by what's going on, so you go up to a unicorn named Billiam and ask him, "Hey! Where am I, and what's up with all the paper airplane chaos?"

Billiam turns to you and replies, "Hi! We're in the Activity Pub! Those paper airplanes are how we communicate here, by sending activities, because the pub is the size of planet! You can't just yell to your friend all the way on the other side of the world, you need paper airplanes!"

That doesn't help at all. Even more confused now, you ask, "Um, what's an activity?"

"Good question. That question you just asked me was an activity. Walking over to me was an activity. Closing your eyes and imagining yourself inside a pub was an activity."

"You're just making me more and more confused."

"An activity is just someone doing something to another thing. Here in the Activity Pub, we call the someone an actor, the something an action, and the another thing an object. Actors do actions to objects. Using fancy jargon, this is called the actor model. Highly scientific, you see. When you asked me that question, the actor was you, the action was asking, and the object was the question. Easy as that."

"OK, but what about those paper airplanes? I don't even understand how unicorns can throw paper airplanes with their hooves."

"So let's say I want to tell my friend Lalani an activity. But what if... she's not even inside the pub right now? This is where those giant centipedes come in. They're servers, since they serve us vintage milk in this pub. Us unicorns each have a server. Some centipedes serve thousands of unicorns, which is why they need to have so many limbs. There are tons of servers out there."

"Couldn't you just have a single centipede serve everyone?"

"No, that's crazy. Centipedes don't have millions of legs. And even if they did, what if the centipede started hating you? Then you'd be really screwed. Anyways, we each have an inbox and outbox with our server. I'll first write down my activity on this piece of paper and who it's to and fold it into an airplane. Then I'll throw my paper airplane to my server, a friendly centipede named Pete, and they'll add it to my outbox since it's an outgoing activity. Finally, they'll throw it to Lalani's server and her server will add it to her inbox. Then later on, Lalani can go ask her server if any activities showed up in her inbox while she was gone, and read them."

"May I ask... are those centipedes... venomous?"

"Of course! All centipedes are venomous! But don't worry, they won't hurt you. Well, just don't mention the birdsite, or they'll eat your brain."

"What? This pub scaring the bejeebers out of me. I'm leaving."

"Don't leave yet! I haven't explained following to you yet!"

"What, are you going to stalk me now?"

"No, following is how you can you receive updates of all the cool activities that your friends are doing. It's super easy. First, you write down a very specific activity: the actor is yourself, the action is following, and the object is the unicorn you'd like to follow, for instance, Lalani. You then do the whole shazaam, and after a bit, Lalani will send you back a very specific activity: the actor is them, the action is accepting the follow, and the object is your follow request. To keep track of follows, the centipedes have a special notebook where they list who's following who. It's as easy as buffalo chicken pizza."

"And what's the point of that whole hassle?"

"Well, it's important that the other unicorn consents to the follow. But here's the cool part. Now every time Lalani does a public activity, her server will copy the airplane and throw copies of it to all the servers of her followers! Then, I can ask my server if any activities for me have showed up!"

"You're making my brain hurt. Wait, there's one thing I don't get. What's stopping me right now from grabbing a sheet of paper, writing down a fake activity with you as the actor, and then impersonating you?"

"First of all, you probably can't throw a paper airplane nearly as well as us unicorns. But we do have a slick and moist way for dealing with this. It's technically isn't part of the protocol, the formal set of rules for the Activity Pub, but everyone just does it this way. For each of my paper airplanes, I lick the paper. Those centipedes have an excellent sense of smell, so just by smelling my breath and the paper, they'll know that activity is from me."

"Ew."

"Hey, it works!"


## Why did it have to be snakes?

A bunch of snakes have visited the Activity Pub and have instantly fallen in love with this zany paper airplane system. They decide to copy it in their own snake language. Here's how they implemented unicorns:
```python
with open('activity', 'rb') as f:
	activity = f.read()

lick = lick_paper()

throw(server, lick, activity)
```

Easy enough, right? The centipede implementation isn't much scarier:
```python
def process_airplane(self):
	activity = self.extract_activity()
	lick = self.extract_lick()
	print(activity)

	verify_lick(lick)

	username = self.get_username()
	if self.activity_for_inbox():
		list_append(username, 'inbox', activity)
		if activity['type'] == 'Accept':
			list_append(username, 'following', activity['actor'])
	elif self.activity_for_outbox():
		list_append(username, 'outbox', activity)
		for to in activity['to']:
			if to == 'followers':
				for follower in get_list(username, 'followers'):
					server_throw(follower, lick, activity)
			else:
				server_throw(to, lick, activity)
		if activity['type'] == 'Accept':
			list_append(username, 'followers', activity['object']['actor'])
```

Hooray! You should go through the code line-by-line and make sure it makes sense, based on the description of the Activity Pub from earlier.

Wait... this code won't even run. Let's fix that! To get there, we first have to iron out some gory technical details.


## The real world

Alright, if your eyes are still closed, you should definitely open them now. Back to the real world!

You might of thought those paper airplanes were just a cute analogy, but the omnipotent and all-powerful ActivityPub spec doesn't require any specific transport mechanism. Unfortunately, even the best paper airplanes in our world can't sail more than tens of meters, so maybe let's try carrier pigeons? Actually, screw that, let's just use HTTP.

But first, we need to talk about the global social graph conspiracy. Actually, it's not a conspiracy, but rather some megalomaniac JSON dressed in a fancy `@context` suit and monocle. (Omninous music plays as JSON-LD enters the chat.) "JSON-LD" is just "JSON" with three extra characters. Likewise, JSON-LD is just JSON with an extra `@context` field that's almost always `https://www.w3.org/ns/activitystreams` for our intents and purposes. Just think of it as JSON.

I've been trying to shield you from the horror and gore of what activities actually look like, but I can't hide it any longer. Here's an activity, in its full JSON-LD glory:
```json
{
	"@context": "https://www.w3.org/ns/activitystreams",
	"type": "Follow",
	"id": "https://centipete.exozy.me/users/billiam.follows/1",
	"actor": "https://centipete.exozy.me/users/billiam.jsonld",
	"object": "https://lala.ni/users/lalani.jsonld",
	"to": "https://lala.ni/users/lalani.jsonld"
}
```

Wait... I thought it would be more monstrous... never mind. That's an activity. Let's break it down. `@context` is just some JSON-LD narcissism. `type` is the action of this activity, in this case, a follow. `actor` is... the actor, I mean, who could possibly even guess that? And `object` is... the object, wow! `to` is the intended recipient of the activity.

Did you notice I forgot one? Yep, `id`, also known as a link, URL, or IRI. (All these words basically mean the same thing, unless you like funky Unicode characters, but that's way out of the scope of this guide.) In ActivityPub, any activity, actor, or object that isn't transient should have an `id`. Basically, if the thing isn't gonna vanish after a few seconds, it needs an `id`. The `id` is just some string telling us where this thing is located and where to fetch this thing, if we ever want to do that. Since we're using HTTP, they're all going to be URLs, but in theory, you could use some centipede-based system if you wanted.

Now what if we try fetching `https://centipete.exozy.me/users/billiam.jsonld`? Let's take a look: (or try it yourself!)
```json
{
	"@context": [
		"https://www.w3.org/ns/activitystreams",
		"https://w3id.org/security/v1"
	],
	"type": "Person",
	"id": "https://centipete.exozy.me/users/billiam.jsonld",
	"preferredUsername": "billiam",
	"name": "Billiam Wender",
	"summary": "Some random unicorn",
	"inbox": "https://centipete.exozy.me/users/billiam.inbox",
	"outbox": "https://centipete.exozy.me/users/billiam.outbox",
	"followers": "https://centipete.exozy.me/users/billiam.followers",
	"following": "https://centipete.exozy.me/users/billiam.following",
	"publicKey": {
		"id": "https://centipete.exozy.me/users/billiam.jsonld#main-key",
		"owner": "https://centipete.exozy.me/users/billiam.jsonld",
		"publicKeyPem": "-----BEGIN PUBLIC KEY-----\nMIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAnPn6SRQ1JTwqFMFJq7Bg\nxMW/4V/HYSjHPbtKL/3jwXOTmOLUz4wr4Ib1IwiKpsguz0uyX1Ljbe3qQzqaCZu5\nhdoGo+uJsanz6yAoLYwETfIFIjqQr3FoIGQxYJDELkhSO8htbOKKoRiVKsjD5x4Q\ncEkZkLAaef8WVh08sEqE3fC4uLmOlSavycbMLmah9UdhljFcaVSQ7qox9HFvKF2e\ni4BmN2WdMGZ8JN20nc0OxoZpBpMArRqF0krjIkhA5/9CvIZEULPQChYL6AcmLS0j\n/yTxE82eFMBBvyeg8ICMRzcbCoN+hVnv3cb/7+BwNdoQjZqt0RG/hhbpc8RKt+YO\n9QIDAQAB\n-----END PUBLIC KEY-----\n"
	}
}
```

Whoa, yikes! That's a lot! This is an actor object, in JSON-LD of course. You can probably somewhat understand most of that, but I'd like to clarify that `publicKey` field. For a brief primer on public-key crypto (as in cryptography, not the kind of scam): Imagine you're a unicorn. Your private key is your super special saliva sauce. It's a unique thing that only you have. Your public key is the smell of your breath, since anyone can come up to you and smell it. Now whenever you author an activity, you'll create a signature, by licking the activity. Only you can create this signature, and other people can verify it's yours based on the smell of your breath. (This is a very bad analogy, but it makes up for that by being fun!)

The real life counterpart of this is HTTP signatures using RSA. Why this? Well, it's not in the ActivityPub spec, but people just decided to do it this way. Billiam includes the public key in his actor object, because he wants everyone to know the smell of his breath. Usually, people use an HTTP signatures library to handle all the crypto stuff, so I won't go over the [fancy details](https://docs.joinmastodon.org/spec/security/#http) here.

Onward! Let's finish the unicorn implementation first. This is an ActivityPub client, since it interacts an ActivityPub centipede, wait, I meant server. The only change we have to do is use `requests.post` for actually sending the activity. Here's what the final code looks like:
```python
from cryptography.hazmat.primitives import hashes, serialization
from cryptography.hazmat.primitives.asymmetric import padding
from base64 import b64encode
from email.utils import formatdate
from requests import post

with open('activity', 'rb') as f:
	activity = f.read()

# Gory technical code to create signature
date = formatdate(usegmt=True)
digester = hashes.Hash(hashes.SHA256())
digester.update(activity)
digest = b64encode(digester.finalize()).decode()
message = f'date: {date}\ndigest: SHA-256={digest}'.encode('utf8')
with open('private.pem', 'rb') as f:
	privkey = serialization.load_pem_private_key(f.read(), None)
signature = b64encode(privkey.sign(message, padding.PKCS1v15(), hashes.SHA256())).decode()
header = f'keyId="https://centipete.exozy.me/users/billiam.jsonld#main-key",headers="date digest",signature="{signature}"'

resp = post('https://centipete.exozy.me/users/billiam.outbox', headers={
	'Date': date, 'Digest': f'SHA-256={digest}', 'Signature': header
}, data=activity)
print(resp)
print(resp.text)
```

Not bad. Now onto the server code!

First, let's try implementing `list_append`. ActivityPub represents a list as an `OrderedCollection`, which is just a fancy wrapper around a JSON array. They look like this:
```json
{
	"@context": "https://www.w3.org/ns/activitystreams",
	"type": "OrderedCollection",
	"id": "https://centipete.exozy.me/users/billiam.following",
	"totalItems": 1,
	"orderedItems": ["https://lala.ni/users/lalani.jsonld"]
}
```

Of course, in your server, you're free to store the list however you want, such as in a database. But if someone asks you for the list, you have to serve it to them in that `OrderedCollection` format. Let's do something stupid: we'll just store the list in a file, directly as an `OrderedCollection`. Then, appending to the list becomes super simple and super inefficient: open the file, load the JSON, modify it, dump it back to the file.
```python
def list_append(username, file, item):
	with open(f'users/{username}.{file}') as f:
		collection = load(f)
	collection['orderedItems'].append(item)
	collection['totalItems'] += 1
	with open(f'users/{username}.{file}', 'w') as f:
		dump(collection, f)
```

To gloss over the details of implementing HTTP servers, we'll just use Python's instant-ramen-style `SimpleHTTPServer`. Let's assume the users' inboxes and outboxes are at endpoints like `/users/billiam.inbox`.

```python
from base64 import b64decode
from cryptography.hazmat.primitives import hashes, serialization
from cryptography.hazmat.primitives.asymmetric import padding
from http.server import SimpleHTTPRequestHandler, ThreadingHTTPServer
from json import dump, load, loads
from re import search
from requests import get, post

# Process requests
# This handler just returns static files for GET requests
class ActivityPubHandler(SimpleHTTPRequestHandler):
	def do_POST(self):
		body = self.rfile.read(int(self.headers['Content-Length']))
		activity = loads(body)
		print(activity)

		# Get the username from the request path using a regex
		username = search('^/users/(.*)\.(in|out)box$', self.path).group(1)

		# Gory technical code to verify signature
		# Get signer public key
		signer = iri_to_actor(search('keyId="(.*?)"', self.headers['Signature']).group(1))
		pubkeypem = signer['publicKey']['publicKeyPem'].encode('utf8')
		pubkey = serialization.load_pem_public_key(pubkeypem, None)
		# Assemble headers
		headers = search('headers="(.*?)"', self.headers['Signature']).group(1)
		message = ''
		for header in headers.split():
			headerval = f'post {self.path}' if header == '(request-target)' else headerval = self.headers[header]
			message += f'{header}: {headerval}\n'
		# Verify HTTP signature
		signature = search('signature="(.*?)"', self.headers['Signature']).group(1)
		pubkey.verify(b64decode(signature), message[:-1].encode('utf8'), padding.PKCS1v15(), hashes.SHA256())
		actor = keyid.removesuffix('#main-key')
		# Make sure activity doer matches HTTP signature 
		if ('actor' in activity and activity['actor'] != signer['id']) or \
		   ('attributedTo' in activity and activity['attributedTo'] != signer['id']) or \
		   ('attributedTo' in activity['object'] and activity['object']['attributedTo'] != signer['id']):
			self.send_response(401)
			return
		
		if self.path.endswith('inbox'):
			# Server sending an incoming activity!
			list_append(username, 'inbox', activity)
			if activity['type'] == 'Accept':
				list_append(username, 'following', activity['actor'])
		elif self.path.endswith('outbox'):
			# Client sending an outgoing activity!
			list_append(username, 'outbox', activity)
			for to in activity['to']:
				if to == 'followers':
					# Tell all my followers!
					with open(f'users/{username}.followers') as f:
						for follower in load(f)['orderedItems']:
							send(follower, self.headers, body)
				else:
					send(to, self.headers, body)
			if activity['type'] == 'Accept':
				list_append(username, 'followers', activity['object']['actor'])
		
		self.send_response(200)
		self.end_headers()

# Run it!
ThreadingHTTPServer(('localhost', 4200), ActivityPubHandler).serve_forever()
```

And that's it! You can check our earlier `process_airplane` implementation and compare to see that this code is really just doing the same exact stuff.

Well, I kinda lied. There's two simple helper functions, `iri_to_actor` and `send` that I didn't go over. The first function `iri_to_actor`, fetches an actor object over HTTP, caches it to disk, and returns it:
```python
from os.path import isfile
from urllib.parse import quote_plus

def iri_to_actor(iri):
	domain = 'https://centipede.exozy.me'
	if domain in iri:
		# User belongs to this server
		username = search(f'^{domain}/users/(.*?)$', iri.removesuffix('#main-key')).group(1)
		actorfile = f'users/{username}'
	else:
		# User belongs to a different server
		actorfile = f'users/{quote_plus(iri.removesuffix("#main-key"))}'
	if not isfile(actorfile):
		with open(actorfile, 'w') as f:
			resp = get(iri, headers={'Accept': 'application/activity+json'})
			print(resp)
			print(resp.text)
			f.write(resp.text)
	with open(actorfile) as f:
		return load(f)
```

The second function simply sends out an activity from one server to its destination server.
```python
def send(to, headers, body):
	actor = iri_to_actor(to)
	headers['Host'] = to.split('/')[2]
	resp = post(actor['inbox'], headers=headers, data=body)
	print(resp)
	print(resp.text)
```

Whew! Now that's it! ActivityPub in just 100 lines of Python.


## The real real world

OK, so you're probably thinking, let's run this thing and test it out! Not so fast. Since we're using HTTP, or more specifically HTTPS, you'll need a domain name and reverse proxy for TLS. If that was just a load of mumbo-jumbo to you, don't worry about it. You can find detailed instructions for running this code [here](https://git.exozy.me/a/fuwuqi). Just beware: it's not easy. It's fun though!

Now would probably be a good time to talk about the elephant in the room: Mastodon. I [like to say](https://social.exozy.me/@a/109712197207851220) that the reference implementation and test suite for ActivityPub is Mastodon. Why? Because there's no official reference implementation and the official test suite has been unmaintained for years. Mastodon is so large that everyone prioritizes compatibility with it and doing things the Mastodon way, such as using WebFinger and HTTP signatures, which aren't even in the ActivityPub spec.

Wait a minute, WebWhat? If you think about it, our current implementation is not-exactly user friendly. Actually, there are probably exactly two people in the world who know how to use it, you and me. One huge problem is your identifier is some long URL! Like if Billiam tells you, shoot me a paper airplane to `https://centipete.exozy.me/users/billiam.jsonld`, you're going to think he's crazy.

Mastodon's solution is to use the WebFinger standard. You get a nice, short cozy username like `@billiam@centipete.exozy.me`, but internally, it's gotta be translated to the long URL version. To do that, simply fetch `https://centipete.exozy.me/.well-known/webfinger?resource=acct:billiam@centipete.exozy.me`, and it'll hand you some JSON:
```json
{
	"subject": "acct:billiam@centipete.exozy.me",
	"links": [
		{
			"rel": "self",
			"type": "application/activity+json",
			"href": "https://centipete.exozy.me/users/billiam.jsonld"
		}
	]
}
```

That's it. Not bad! You can even try it out yourself by putting `@billiam@centipete.exozy.me` into the search bar on Mastodon.

Also, since you're probably figured out by now that this is a weird ActivityPub guide, there's one key word I've not used yet: federation. But actually, you already know what it means, you just don't know that you know what it means yet. Federation is simply this 3-hop paper airplane thing: first, you send the activity to your server, which gets sent to the recipient's server, and then the recipient checks their server for the message. Instead of a single server, there are thousands of servers!

Now ActivityPub servers aren't just limited to storing and sending activities. The activities can cause stuff to happen! You can send an activity to a [PeerTube](https://joinpeertube.org/) server to comment on a video! You can send an activity to a [Forgejo](https://forgejo.org/) server to create an issue or pull request for a repository! The possibilities are endless! This is the magic of the fediverse: the global network of all centipedes, err, I meant ActivityPub servers.


## What is ActivityPub?

For a guide to ActivityPub, I feel kind of bad for not having a one-sentence explanation for what ActivityPub is. (And no, it's not a mystical pub where unicorns throw around paper airplanes) In the fewest words possible, you should think of ActivityPub as a flexible modeling protocol for actions in a decentralized network.

I should probably also revisit the spec again. Plot twist, there are actually three specs!

The first spec, [Activity Streams 2.0](https://www.w3.org/TR/activitystreams-core/), describes in minute detail exactly how the JSON-LD format for activities works and how to use this format to model actions. The second spec, [Activity Vocabulary](https://www.w3.org/TR/activitystreams-vocabulary/), describes some useful types of activites, actors, and objects that your implementation should support, such as `Follow` activities and `Person` actors. The last spec, everyone's favorite, is [ActivityPub], which specifies how federation works, with all the inbox and outbox fun. They're basically modeling, vocabulary, and behavior respectively.

Plot twist again! ActivityPub is actually two protocols! There's the server-to-server (S2S) protocol when one server sends an activity to the recipient's server, and the client-to-server (C2S) protocol for when a client sends an activity to their own server. S2S is centipedes throwing paper airplanes. C2S is unicorns throwing paper airplanes and checking their inbox for cool new stuff. Unfortunately, Mastodon has some kind of grudge against C2S and only implements S2S, so being the amazing role model it is, everyone else copied this lack of a feature and also only has S2S support. As far as I know, this guide, [Pleroma](https://pleroma.social/), and [FedBOX](https://github.com/go-ap/fedbox) are the only servers out there that actually implement the other half of ActivityPub. Everyone else just drank the Mastodon API kool-aid. (One major difference between this guide and Mastodon is that HTTP signatures are generated by the server in Mastodon.)


## Make something!

You've finally trudged to the end of this guide, but the ActivityPub fun doesn't stop here. With your newfound ActivityPub superpowers, go out there and make something!

Here are some resources that might be helpful. As an `OrderedCollection`, of course.
```json
{
	"@context": "https://www.w3.org/ns/activitystreams",
	"type": "OrderedCollection",
	"totalItems": 6,
	"orderedItems": [
		"https://blog.joinmastodon.org/2018/06/how-to-implement-a-basic-activitypub-server/",
		"https://blog.joinmastodon.org/2018/07/how-to-make-friends-and-verify-requests/",
		"https://git.exozy.me/a/fuwuqi",
		"https://tinysubversions.com/notes/reading-activitypub/",
		"https://flak.tedunangst.com/post/ActivityPub-as-it-has-been-understood",
		"https://codeberg.org/forgejo/forgejo/issues/59#contributing"
	]
}
```

As a final challenge, try replying to [this post](https://social.exozy.me/@a/109718407634106530) using your own ActivityPub server implemenation. Make the unicorns proud! Seeya in the fediverse!

