If you want to load some configurations specific to your tenant you can do something like this

    #config/initalizers/country_specific.rb
    COUNTRY_CONFIGS = YAML.load_file(Rails.root.join('config', 'country_specific' , 'config.yml'))

    #config/country_specific/config.yml
    da:
      site_name: Boligsurf.dk
    se:
      site_name: Bostadssurf.se

    #app/controllers/application_controller
    class ApplicationController < ActionController::Base
      protect_from_forgery
      before_filter :set_country_config
    ......
      def set_country_config
        $COUNTRY_CONFIG = COUNTRY_CONFIGS['da'] #As default as some strange domains also refer this site, we'll just catch em as danish
        $COUNTRY_CONFIG = COUNTRY_CONFIGS['se'] if request.host.include? 'bostadssurf'
      end

     #Somewhere in your code
     puts "Welcome to #{$COUNTRY_CONFIG['site_name']}"

In the config.yml we keep various stuff like google analytics id and webmaster tools for each tenant. Also contact email, currency etc.
    

    