##Tokenize

This Tokenize package contains two functions that are extremely fast and efficient at tokenizing text. No regular expressions are used. No memory leaked (all tokens returned are copies.) The whole thing requires only two loops of the data, the first for normalization and accent removal, the second for everything else.

##Features

 1. UTF8 normalization
 2. Accent removal, e.g. á -> a
 3. Special characters converted to their common form, e.g. æ -> e
 4. Lowercasing
 5. Hypenated words split
 6. Contractions removed, e.g. l'histoire -> histoire
 7. Resulting tokens contain only letters a-z and digits 0-9
 8. All UTF8 scripts are supported.

For example:

    Et l'Histore de l'amitè.
	
Becomes

    et
    histore
    de
    amite

##Installation

    go get github.com/AlasdairF/Tokenize

##Parameters

The optional parameters are:

    lowercase
Converts all letters to lowercase.

    stripAccents
Removes accents, e.g. á -> a.

    stripContractions
Removes contractions, e.g. `l'histore` to `histore`.

    stripNumbers
Removes all numbers.

    stripForeign
Removes all non ASCII characters after accent removal (i.e. it leaves only A-Z-a-z0-9).

Recommended settings for tokenization of English are: `lowercase, stripAccents, stripForeign`

Recommended settings for tokenization of continental European languages are: `lowercase, stripAccents, stripContractions, stripForeign`

Recommended settings for tokenization of international scripts are: `lowercase, stripContractions`

All non-letters and non-numbers, such as punctuation and whitespace are always stripped.

##AllInOne

The first function is AllInOne, two parameters are required. The first is the []byte data to process, the second is the function for what to with each token.

For example, if you want to put all words into a slice then you would use:

    tokens := make([][]byte, 0, 100)
    
    wordfn := func(word []byte) {
    	tokens = append(tokens, word)
    }
    
    lowercase, stripAccents, stripContractions, stripNumbers, stripForeign := true, true, true, false, true
    tokenize.AllInOne(data, wordfn, lowercase, stripAccents, stripContractions, stripNumbers, stripForeign)

##Paginate

Paginate is the same as AllInOne but it also recognizes custom page breaks. Four parameters are required. The first is the []byte data to process, the second is the page break marker as []byte, the third is the function for what to do with each token, the fourth is the function for what to do whenever a page break marker is reached. Please note that the page break marker itself should contain only single-byte characters (ASCII), I usually use `{#}` as the page break marker.

For example:

    pages := make([][][]byte, 0, 10)
    tokens := make([][]byte, 0, 100)
	
	wordfn := func(word []byte) {
    	tokens = append(tokens, word)
    }
	pagefn := func() {
		pages = append(pages, tokens)
		tokens = make([][]byte, 0, 100)
    }
    
    lowercase, stripAccents, stripContractions, stripNumbers, stripForeign := true, true, true, false, true
    tokenize.Paginate(data, []byte("[newpage]"), wordfn, pagefn, lowercase, stripAccents, stripContractions, stripNumbers, stripForeign)
