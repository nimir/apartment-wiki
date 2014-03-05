If you want to select your database, and maybe tenant specific config you can do something like this

`#config/initializers/console_ask_for_lang.rb`

`if defined?(Rails::Console)`
  `def switch_lang`
    `while true`
      `print "Select your locale:\n"`
      `print "1: Dk\n"`
      `print "2: Sv\n"`
      `case gets.strip`
        `when '1'`
          `$COUNTRY_CONFIG = COUNTRY_CONFIGS['da']`
          `puts 'Selected dk!'`
          `break`
        `when '2'`
          `$COUNTRY_CONFIG = COUNTRY_CONFIGS['se']`
          `Apartment::Database.switch("my_database_se_#{Rails.env.development? ? 'development' : 'production'}")`
          `puts 'Selected sv!'`
          `break`
        `else`
          `c = 1`
      `end`
      `break if !c`
    `end`
  `end`
  `switch_lang()`
`end`