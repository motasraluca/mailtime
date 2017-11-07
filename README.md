# Mailtime

Makes sending mail with Rails ~great again~ more bearable by allowing you to manage the mail from ActiveRecord as well as log it.

### Not using ActionMailer?

No problem, but it's untested. Check out **Without ActionMailer** below.

## Mailtime...

* Tells you (via ActiveRecord object) that a `thing` has been mailed, what they were mailed, and under what context
* (optionally) allows you to completely manage your ActionMailer templates (and optional layouts) from ActiveRecord

## Installation

Add to your Gemfile: `gem 'mailtime'`

Run `bundle install`

Next, run the generator to copy over migrations and initializer: 

`$ rails g mailtime:install`

And do the migrate,

`rake db:migrate`

## Usage

Add `mailtimer NAME [options]` to each class that has an `email` attribute associated with it (or, rather, is emailable)

* NAME is the attribute (within ActiveRecord) that holds the object's email address. Default `email`
* [options] is your favorite optional hash but is mostly useless as of writing.

## Configuration

`mailtimer :email_address, :fields => [:to, :cc, :bcc]`

## How it works

Mailtime hooks into `ActionMailer` with an `Interceptor`. It injects some methods into `ActionMailer::Base` which allows
it to to extract some metadata to associate with the Mailtime objects: `Mailtime::Log`, `Mailtime::Template`, and
`Mailtime::Layout`.

Mailtime requires no additional configuration or special sending methods. It only cares that you're using ActionMailer.
This could be easily changed to suit your application's needs.

### Without ActionMailer

Before you attempt mail delivery, hook into `Mailtime::Interceptor` and pass the closest-thing-to-a-mail-object object.
This object must respond to `mailtime_metadata` and be an object that contains `mailer_class`, `mailer_action`, and
`action_variables` — a hash that represents any context (instance variables) that were used to build the mail. [Example](lib/mailtime/action_mailer/metadata.rb).

Pretending closest-thing-to-a-mail-object object is called `mail_api_call` from class `MailApiCall`...

```Ruby
class MailApiCall

  def mailtime_metadata
    OpenStruct.new(
      :mailer_class => self.class.to_s, 
      :mailer_action => self.action_name,
      :action_variables => {:user => @user}
    )
  end

end
```

### Views

Mailtime doesn't ship with views or authentication for said non-existent views. 