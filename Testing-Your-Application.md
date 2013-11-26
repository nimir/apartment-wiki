Here are a few tips to help you get over the hurdles of testing a multi-tenant application.

We use Rspec at Influitive, I'm *assuming* there are similar facilities for test/unit or mini-test (ie setup/teardown), if anyone wants to contribute the equivalent that'd be great.

The number one thing that has helped us in testing is to create a single tenant before the whole test suite runs and switch to that tenant for the run of your tests. 

> TL;DR You want to ensure your db is clean at the start of the suite and that a tenant exists that you can create your fixture/factory models within. Before each test runs you should switch into that tenant so that way you can guarantee a model to exist for that tenant at the time that the test runs

Our `spec_helper.rb` looks like so:

```ruby
RSpec.configure do |config|
  config.before(:suite) do
    # Clean all tables to start
    DatabaseCleaner.clean_with :truncation
    # Use transactions for tests
    DatabaseCleaner.strategy = :transaction
    # Truncating doesn't drop schemas, ensure we're clean here, app *may not* exist
    Apartment::Database.drop('app') rescue nil
    # Create the default tenant for our tests
    Company.create!(name: 'Influitive Corp.', subdomain: 'app')
  end

  config.before(:each) do
    # Start transaction for this test
    DatabaseCleaner.start
    # Switch into the default tenant
    Apartment::Database.switch 'app'
  end

  config.after(:each) do
    # Reset tentant back to `public`
    Apartment::Database.reset
    # Rollback transaction
    DatabaseCleaner.clean
  end
end
```

Note that we use a `Company` object as our tenant model for storing each tenant and any associated config with it. This `Company` model has an `after_commit` that actually creates an Apartment tenant based on the subdomain.