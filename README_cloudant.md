# couchmail.py

**Very** early stage email archiver for Apache CouchDB
and Cloudant.  This README assumes you'll be using a Cloudant 
account and DB to to archive and search through your mail.

## Installation

Copy `config.sample.ini` to `config.ini`.
Customize it.  You may need to use an application-specific 
password for GMail.  Here is what a config should look like


	[imap]
	host = imap.gmail.com
	user = <USERNAME>@gmail.com
	mailbox = Inbox
	password = <Password>
	
	[couch]
	server = http://<USERNAME>:<PASSWORD>@<USERNAME>.cloudant.com/
	db = mail
	; if doing multiple accounts per couch, you
	; might want to sha1() the email address to
	; be the mailbox name.

Then you'll want to make sure you have all the libraries.

    $ pip install -r requirements.txt
    $ python import.py

Hope for the best!

It *should* export the number of emails you specify,
store them by their `Message-ID` header value (or
timestamp...if `Message-ID` is missing).

Now, you'll want to install the design and index documents so you 
can search through your imported emails.

## Design Doc (aka CouchApp)

    $ cd couchapp
    $ couchapp push . http://<USERNAME>:<PASSWORD>@<USERNAME>.cloudant.com/mail

Once you've pushed, you should be able to see your data and the design docs in the 'mail' database on your cloudant account.  You can now perform some searches:

#### Full-Text Search

The Design Doc includes basic MapReduce for counting things (see Futon, Fauxton, or the Cloudant Dashboard for more).

It also includes some [Cloudant](http://cloudant.com/) specific Full-Text Search indexes. These indexes let you search email addresses, subject lines, and message bodies.

Here are some examples…

Search for "fauxton":

```
/mail/_design/couchmail/_search/mail?q=fauxton
```

Count those results by `subject`, `to`, and `from`:

```
/mail/_design/couchmail/_search/mail?q=fauxton&counts=["subject", "to", "from"]
```

Now drilldown those results to just what I've posted:

```
/mail/_design/couchmail/_search/mail?q=fauxton&counts=["subject", "to", "from"]&drilldown=["from", "Benjamin Young <byoung@bigbluehat.com>"]
```

## License

[Apache License 2.0](http://www.apache.org/licenses/LICENSE-2.0.html)
