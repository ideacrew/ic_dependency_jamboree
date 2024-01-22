---
title: Enroll Rails 6 Upgrade
date: 2024-01-22
linkTitle: Enroll Rails 6 Upgrade
author: Trey Evans & Sai Kumar Kotagiri
---

This is our attempt to upgrade to Ruby On Rails 6 for Enroll.

First, we attempted an upgrade from our current version, 5.2.4.3, to the most recent patch version, 5.2.8.1.

## Strange Spec Failure

The first spec failure we encountered had to do with storage of rack session ids in the EventLog concern.

We got the following spec failure:

```shell
  1) Operations::Eligible::MigrateBenefitSponsorCatalog benefit sponsor catalog migrations when application created after sponsor eligibility when sponsorship passed should migrate benefit sponsor catalog
     Failure/Error: Success(event.publish)

     ArgumentError:
       Unsupported value "18959d34d8b7d5f85f268664d351e674" of type Rack::Session::SessionId
     # /usr/local/bundle/ruby/2.6.0/gems/amq-protocol-2.3.2/lib/amq/protocol/table_value_encoder.rb:74:in `encode'
     # /usr/local/bundle/ruby/2.6.0/gems/amq-protocol-2.3.2/lib/amq/protocol/table.rb:43:in `block in encode'
     # /usr/local/bundle/ruby/2.6.0/gems/amq-protocol-2.3.2/lib/amq/protocol/table.rb:34:in `each'
     # /usr/local/bundle/ruby/2.6.0/gems/amq-protocol-2.3.2/lib/amq/protocol/table.rb:34:in `encode'
     # /usr/local/bundle/ruby/2.6.0/gems/amq-protocol-2.3.2/lib/amq/protocol/table.rb:41:in `block in encode'
     # /usr/local/bundle/ruby/2.6.0/gems/amq-protocol-2.3.2/lib/amq/protocol/table.rb:34:in `each'
     # /usr/local/bundle/ruby/2.6.0/gems/amq-protocol-2.3.2/lib/amq/protocol/table.rb:34:in `encode'
     # /usr/local/bundle/ruby/2.6.0/gems/amq-protocol-2.3.2/lib/amq/protocol/table.rb:41:in `block in encode'
     # /usr/local/bundle/ruby/2.6.0/gems/amq-protocol-2.3.2/lib/amq/protocol/table.rb:34:in `each'
     # /usr/local/bundle/ruby/2.6.0/gems/amq-protocol-2.3.2/lib/amq/protocol/table.rb:34:in `encode'
     # /usr/local/bundle/ruby/2.6.0/gems/amq-protocol-2.3.2/lib/amq/protocol/client.rb:1409:in `encode_headers'
     # /usr/local/bundle/ruby/2.6.0/gems/amq-protocol-2.3.2/lib/amq/protocol/client.rb:1504:in `block in encode_properties'
     # /usr/local/bundle/ruby/2.6.0/gems/amq-protocol-2.3.2/lib/amq/protocol/client.rb:1503:in `each'
     # /usr/local/bundle/ruby/2.6.0/gems/amq-protocol-2.3.2/lib/amq/protocol/client.rb:1503:in `encode_properties'
     # /usr/local/bundle/ruby/2.6.0/gems/amq-protocol-2.3.2/lib/amq/protocol/client.rb:1813:in `encode'
     # /usr/local/bundle/ruby/2.6.0/gems/bunny-2.22.0/lib/bunny/channel.rb:631:in `basic_publish'
     # /usr/local/bundle/ruby/2.6.0/gems/bunny-2.22.0/lib/bunny/exchange.rb:141:in `publish'
     # /usr/local/bundle/ruby/2.6.0/bundler/gems/event_source-e368d614f37c/lib/event_source/protocols/amqp/bunny_exchange_proxy.rb:52:in `publish'
     # /usr/local/bundle/ruby/2.6.0/bundler/gems/event_source-e368d614f37c/lib/event_source/publish_operation.rb:29:in `call'
     # /usr/local/bundle/ruby/2.6.0/bundler/gems/event_source-e368d614f37c/lib/event_source/publisher.rb:72:in `publish'
     # /usr/local/bundle/ruby/2.6.0/bundler/gems/event_source-e368d614f37c/lib/event_source/event.rb:73:in `publish'
     # ./app/domain/operations/event_logs/trackable_event.rb:72:in `publish'
     # /usr/local/bundle/ruby/2.6.0/gems/dry-monads-1.3.5/lib/dry/monads/do.rb:114:in `block in publish'
     # /usr/local/bundle/ruby/2.6.0/gems/dry-monads-1.3.5/lib/dry/monads/do/mixin.rb:40:in `call'
     # /usr/local/bundle/ruby/2.6.0/gems/dry-monads-1.3.5/lib/dry/monads/do.rb:114:in `publish'
     # ./app/domain/operations/event_logs/trackable_event.rb:26:in `call'
     # /usr/local/bundle/ruby/2.6.0/gems/dry-monads-1.3.5/lib/dry/monads/do.rb:114:in `block in call'
     # /usr/local/bundle/ruby/2.6.0/gems/dry-monads-1.3.5/lib/dry/monads/do/mixin.rb:40:in `call'
     # /usr/local/bundle/ruby/2.6.0/gems/dry-monads-1.3.5/lib/dry/monads/do.rb:114:in `call'
     # ./components/benefit_sponsors/app/domain/benefit_sponsors/operations/benefit_sponsorships/shop_osse_eligibilities/create_shop_osse_eligibility.rb:154:in `publish_event'
     # /usr/local/bundle/ruby/2.6.0/gems/dry-monads-1.3.5/lib/dry/monads/do.rb:114:in `block in publish_event'
     # /usr/local/bundle/ruby/2.6.0/gems/dry-monads-1.3.5/lib/dry/monads/do/mixin.rb:40:in `call'
     # /usr/local/bundle/ruby/2.6.0/gems/dry-monads-1.3.5/lib/dry/monads/do.rb:114:in `publish_event'
     # ./components/benefit_sponsors/app/domain/benefit_sponsors/operations/benefit_sponsorships/shop_osse_eligibilities/create_shop_osse_eligibility.rb:30:in `call'
     # /usr/local/bundle/ruby/2.6.0/gems/dry-monads-1.3.5/lib/dry/monads/do.rb:114:in `block in call'
     # /usr/local/bundle/ruby/2.6.0/gems/dry-monads-1.3.5/lib/dry/monads/do/mixin.rb:40:in `call'
     # /usr/local/bundle/ruby/2.6.0/gems/dry-monads-1.3.5/lib/dry/monads/do.rb:114:in `call'
     # ./spec/domain/operations/eligible/migrate_benefit_sponsor_catalog_spec.rb:64:in `block (3 levels) in <main>'
     # ./spec/domain/operations/eligible/migrate_benefit_sponsor_catalog_spec.rb:77:in `block (3 levels) in <main>'
     # ./spec/domain/operations/eligible/migrate_benefit_sponsor_catalog_spec.rb:119:in `block (3 levels) in <main>'
     # ./spec/rails_helper.rb:65:in `block (2 levels) in <top (required)>'
     # ./spec/spec_helper.rb:116:in `block (2 levels) in <top (required)>'
```

Strangely enough, the failure only manifested when two specs were run in tandem and in a particular order:

```bash
RAILS_ENV=test bundle exec rspec spec/controllers/products/qhp_controller_spec.rb spec/domain/operations/eligible/migrate_benefit_sponsor_catalog_spec.rb
```

It seems that we have several issues here:
1. This spec set **only** fails once we upgrade the gem versions
2. It looks like the EventLog is having an issue encoding the session 'left alive' by the previous controller spec, although we have tried a 'sign out' after the controller specs and it didn't seem to work
3. We've placed multiple DB cleans around all of these specs - should we check and see if db cleaner actually wipes the sessions table?