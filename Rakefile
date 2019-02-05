require 'rake/clean'
require 'dotenv/tasks'
require_relative 'lib/import'

CLOBBER.include('data/*.json', 'source/images/denali/*')

namespace :import do
  directory 'data'
  directory 'source/images/denali'

  task :set_up_directories => ['data', 'source/images/denali']

  desc 'Import latest Denali photos'
  task :denali => [:dotenv, :set_up_directories] do
    begin
      puts '== Importing Denali photos'
      start_time = Time.now
      denali = Import::Denali.new(ENV['DENALI_URL'], ENV['DENALI_COUNT'].to_i)
      denali.get_photos
      puts "Completed in #{Time.now - start_time} seconds"
    rescue => e
      abort "Failed to import Denali photos: #{e}"
    end
  end

  desc 'Import data from Goodreads'
  task :goodreads => [:dotenv, :set_up_directories] do
    begin
      puts '== Importing data from Goodreads'
      start_time = Time.now
      goodreads = Import::Goodreads.new(ENV['GOODREADS_RSS_FEED'], ENV['GOODREADS_COUNT'].to_i)
      goodreads.get_books
      puts "Completed in #{Time.now - start_time} seconds"
    rescue => e
      abort "Failed to import Goodreads data: #{e}"
    end
  end

  desc 'Import data from Untappd'
  task :untappd => [:dotenv, :set_up_directories] do
    begin
      puts '== Importing data from Untappd'
      start_time = Time.now
      untappd = Import::Untappd.new(ENV['UNTAPPD_USERNAME'], ENV['UNTAPPD_CLIENT_ID'], ENV['UNTAPPD_CLIENT_SECRET'], ENV['UNTAPPD_COUNT'].to_i)
      untappd.get_beers
      puts "Completed in #{Time.now - start_time} seconds"
    rescue => e
      abort "Failed to import Untappd data: #{e}"
    end
  end

  desc 'Import data from Spotify'
  task :spotify => [:dotenv, :set_up_directories] do
    begin
      puts '== Importing Spotify data'
      start_time = Time.now
      music = Import::Spotify.new(ENV['SPOTIFY_REFRESH_TOKEN'])
      music.get_albums
      puts "Completed in #{Time.now - start_time} seconds"
    rescue => e
      abort "Failed to import Music data: #{e}"
    end
  end
end

task :import => %w{
  clobber
  import:goodreads
  import:untappd
  import:spotify
  import:denali
}

desc 'Import content and build the site'
task :build => [:dotenv, :import] do
  puts '== Building the site'
  system('middleman build')
end

desc 'Sync the site to S3'
task :sync do
  puts '== Syncing with S3'
  system('middleman s3_sync')
end

desc 'Publishes the site'
task :publish => [:build, :sync]

