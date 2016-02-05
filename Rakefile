#!/usr/bin/env rake

require "capybara"
require "capybara/dsl"
require "capybara/poltergeist"
require "csv"

Capybara.default_driver = :poltergeist
Capybara.run_server = false
include Capybara::DSL

namespace :yalp do
  desc "Scrape Yalp for leads and export them to CSV"
  task :scrape do |args|

    puts "Visiting #{search_url}"
    visit search_url

    @lead_urls = []
    count = 0
    while true do
      count += 1
      puts "Gathering lead URLs from page #{count}"
      all(".regular-search-result .biz-name").each do |result|
        @lead_urls << "http://www.yelp.com#{result[:href]}"
      end

      if has_css?(".next")
        find(".next").click
        sleep 1
      else
        puts "Done gathering lead URLs"
        break
      end
    end

    puts "Starting to gather lead information"

    @leads = []

    @lead_urls.each_with_index do |lead_url, count|
      puts "Gathering information for lead #{count + 1} of #{@lead_urls.count}"
      visit lead_url

      lead = {}
      lead[:name] = text_from(".biz-page-title")
      lead[:url] = text_from(".biz-website a")
      lead[:phone] = text_from(".biz-phone")

      if has_css?("address")
        lead[:street] = text_from("[itemprop=streetAddress]")
        lead[:city] = text_from("[itemprop=addressLocality]")
        lead[:state] = text_from("[itemprop=addressRegion]")
        lead[:zip] = text_from("[itemprop=postalCode]")
      end

      if has_link?("Learn more about #{lead[:name]}")
        click_on "Learn more about #{lead[:name]}"
        lead[:owner] = text_from(".meet-business-owner .user-display-name")
      end

      @leads << lead
    end

    puts "Writing lead information to #{output}"
    CSV.open(output, "wb") do |csv|
      csv << ["Company", "Owner", "Website", "Phone", "Street", "City", "State", "Zip"]
      @leads.each do |lead|
        csv << [lead[:name], lead[:owner], lead[:url], lead[:phone], lead[:street], lead[:city], lead[:state], lead[:zip]]
      end
    end
  end

  def encode_param(text)
    URI.escape(text).gsub("%20", "+")
  end

  def save_page_tmp
    save_page("page-#{Time.now.strftime("%Y%m%d%H%M%S%L")}.html")
  end

  def text_from(css)
    if has_css?(css)
      find(css).text.strip
    else
      nil
    end
  end

  def search
    return @_search if defined?(@_search)
    if ENV["search"].nil?
      raise_incorrect_usage
    else
      @_search = ENV["search"]
    end
  end

  def encoded_search
    return @_encoded_search if defined?(@_encoded_search)
    @_encoded_search = encode_param(search)
  end

  def location
    return @_location if defined?(@_location)
    if ENV["location"].nil?
      raise_incorrect_usage
    else
      @_location = ENV["location"]
    end
  end

  def encoded_location
    return @_encoded_location if defined?(@_encoded_location)
    @_encoded_location = encode_param(location)
  end

  def search_url
    return @_search_url if defined?(@_search_url)
    @_search_url = "http://www.yelp.com/search?find_desc=#{encoded_search}&find_loc=#{encoded_location}"
  end

  def output
    return @_output if defined?(@_output)
    if ENV["output"].nil?
      @_output = "leads.csv"
    else
      @_output = ENV["output"]
    end
  end

  def raise_incorrect_usage
    raise ArgumentError, "Please specify a location\nUsage: rake yalp:scrape location='City, State' search='Business Type' output='leads.csv'"
  end
end
