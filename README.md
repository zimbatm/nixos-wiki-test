# NixOS wiki test

This is a test to convert the NixOS MediaWiki into Jekyll and see how it goes.

## Import 

I used https://github.com/philipashlock/mediawiki-to-markdown to convert the
MediaWiki dump into Markdown+frontmatter pages. That makes the import
reproductible.

    php convert.php \
          --filename=wiki-20160212.xml \
          --output=$PWD \
          --format=markdown_github


