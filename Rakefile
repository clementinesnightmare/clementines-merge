require 'bundler'
Bundler.setup

require 'fileutils'
require 'json'

COLLECTIONS = [
  'Aerosol',
  'Ciaran Black',
  'Clementine',
  'Flicker',
  'Ghaxx',
  'Jack Carver',
  'Morde',
  'Stagg',
  'Twee',
  'Vespyre'
]

COLLECTION_DIR = "collection"
IMG_DIR = "image"
META_DIR = "json"

namespace :collection do
  desc "generate collection metadata"
  task :generate_metadata do
    collectionMetaDir = File.join(COLLECTION_DIR, META_DIR)
    output = []
    5000.times do |i|
      j = JSON.parse(File.read(File.join(COLLECTION_DIR, META_DIR, "#{i}.json")))
      output.push(j)
    end
    File.write(File.join(COLLECTION_DIR, META_DIR, "_metadata.json"), JSON.pretty_generate(output))
  end

  desc "shuffle collection"
  task :shuffle do
    # Shuffling code.
    puts "Already shuffled."
    exit(0)

    Dir.mkdir(COLLECTION_DIR) unless File.exists?(COLLECTION_DIR)
    collectionImgDir = File.join(COLLECTION_DIR, IMG_DIR)
    Dir.mkdir(collectionImgDir) unless File.exists?(collectionImgDir)
    collectionMetaDir = File.join(COLLECTION_DIR, META_DIR)
    Dir.mkdir(collectionMetaDir) unless File.exists?(collectionMetaDir)

    iterCol = COLLECTIONS

    5000.times do |i|
      if i % 10 == 0
        iterCol = iterCol.sample(10)
      end

      randChar = iterCol.unshift(iterCol.pop).first
      jsonDirGlob = File.join(randChar, META_DIR, "*.json")
      randJsonFile = Dir.glob(jsonDirGlob).sample
      
      randJson = JSON.parse(File.read(randJsonFile))
      imageExt = randJson["image"].include?(".mp4") ? "mp4" : "png"
      associatedFile = randJsonFile.sub(META_DIR, IMG_DIR).sub("json", imageExt)
      
      puts randJsonFile
      puts associatedFile
      
      FileUtils.mv(associatedFile, File.join(collectionImgDir, "#{i}.#{imageExt}"))
      newJsonFile = File.join(collectionMetaDir, "#{i}.json")
      FileUtils.mv(randJsonFile, newJsonFile)

      correctedJson = randJson
      correctedJson["name"] = correctedJson["name"].sub(/\d+/, i.to_s)
      correctedJson["image"] = correctedJson["image"].sub(/\d+/, i.to_s)

      correctedJson["attributes"].each_with_index do |obj, idx|
        if obj["trait_type"] == "Character"
          correctedJson["attributes"][idx]["trait_type"] = "Skin"
        end
      end

      correctedJson["attributes"].unshift({ "trait_type": "Character", "value": randChar })

      puts JSON.pretty_generate(correctedJson)

      File.write(newJsonFile, JSON.pretty_generate(correctedJson))
    end
  end

  desc "normalize numbering for images"
  task :normalize do
    # Original number normalization code.
    puts "Already normalized."
    exit(0)

    COLLECTIONS.each do |char|
      newDir = "#{char}-ordered"
      Dir.mkdir(newDir) unless File.exists?(newDir)
      Dir.mkdir("#{newDir}/#{IMG_DIR}") unless File.exists?("#{newDir}/#{IMG_DIR}")
      Dir.mkdir("#{newDir}/#{META_DIR}") unless File.exists?("#{newDir}/#{META_DIR}")

      Dir.glob("#{char}/#{IMG_DIR}/*.png").sort.each_with_index do |imgFile, i|
        jsonFile = imgFile.gsub(IMG_DIR, META_DIR).gsub("png", "json")
        FileUtils.cp(imgFile, "#{newDir}/#{IMG_DIR}/#{i}.png")

        meta = JSON.parse(File.read(jsonFile))
        newMeta = {
          name: "#{char} ##{i}",
          image: "ipfs://NewUriToReplace/#{i}.png",
          attributes: meta["attributes"]
        }
        
        File.write("#{newDir}/#{META_DIR}/#{i}.json", JSON.pretty_generate(newMeta))
      end
    end
  end

  desc "info"
  task :info do
    puts "Current Characters: \n" + COLLECTIONS.join(", ") + "\n\n"
    puts "Character Count: " + COLLECTIONS.length.to_s
  end
end

task :default => 'collection:info'
