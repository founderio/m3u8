<!--*- mode:markdown -*-->
M3U8
====

This is the most complete opensource library for parsing and generating of M3U8 playlists
used in HTTP Live Streaming (Apple HLS) for internet video translations.

M3U8 is simple text format and parsing library for it must be simple too. It does not offer
ways to play HLS or handle playlists over HTTP. So library features are:

* Support HLS specs up to version 5 of the protocol.
* Parsing and generation of master-playlists and media-playlists.
* Autodetect input streams as master or media playlists.
* Offer structures for keeping playlists metadata.
* Encryption keys support for use with DRM systems like [Verimatrix](http://verimatrix.com) etc.
* Support for non standard [Google Widevine](http://www.widevine.com) tags.

The library covered by BSD 3-clause license. See [LICENSE](LICENSE) for the full text.
Versions 0.8 and below was covered by GPL v3. License was changed from the version 0.9 and upper.

See the list of the library authors at [AUTHORS](AUTHORS) file.


Project status
---------------

This is a fork of <https://github.com/grafov/m3u8> maintained for my own personal needs.
Pull Requests are welcome, but issues not pertaining to my own needs may be ignored.


Install
-------

	go get github.com/founderio/m3u8

or get releases from https://github.com/founderio/m3u8/releases

Documentation [![GoDoc](https://godoc.org/github.com/founderio/m3u8?status.svg)](https://pkg.go.dev/github.com/founderio/m3u8)
-------------

Package online documentation (examples included) available at:

* <http://pkg.go.dev/github.com/founderio/m3u8>

Supported by the HLS protocol tags and their library support explained in [M3U8 cheatsheet](M3U8.md).

Examples
--------

Parse playlist:

```go
	f, err := os.Open("playlist.m3u8")
	if err != nil {
		panic(err)
	}
	p, listType, err := m3u8.DecodeFrom(bufio.NewReader(f), true)
	if err != nil {
		panic(err)
	}
	switch listType {
	case m3u8.MEDIA:
		mediapl := p.(*m3u8.MediaPlaylist)
		fmt.Printf("%+v\n", mediapl)
	case m3u8.MASTER:
		masterpl := p.(*m3u8.MasterPlaylist)
		fmt.Printf("%+v\n", masterpl)
	}
```

Then you get filled with parsed data structures. For master playlists you get ``Master`` struct with slice consists of pointers to ``Variant`` structures (which represent playlists to each bitrate).
For media playlist parser returns ``MediaPlaylist`` structure with slice of ``Segments``. Each segment is of ``MediaSegment`` type.
See ``structure.go`` or full documentation (link below).

You may use API methods to fill structures or create them manually to generate playlists. Example of media playlist generation:

```go
	p, e := m3u8.NewMediaPlaylist(3, 10) // with window of size 3 and capacity 10
	if e != nil {
		panic(fmt.Sprintf("Creating of media playlist failed: %s", e))
	}
	for i := 0; i < 5; i++ {
		e = p.Append(fmt.Sprintf("test%d.ts", i), 6.0, "")
		if e != nil {
			panic(fmt.Sprintf("Add segment #%d to a media playlist failed: %s", i, e))
		}
	}
	fmt.Println(p.Encode().String())
```

Custom Tags
-----------

M3U8 supports parsing and writing of custom tags. You must implement both the `CustomTag` and `CustomDecoder` interface for each custom tag that may be encountered in the playlist. Look at the template files in `example/template/` for examples on parsing custom playlist and segment tags.

Library structure
-----------------

Library has compact code and bundled in three files:

* `structure.go` — declares all structures related to playlists and their properties
* `reader.go` — playlist parser methods
* `writer.go` — playlist generator methods

Each file has own test suite placed in `*_test.go` accordingly.

Related links
-------------

* http://en.wikipedia.org/wiki/M3U
* http://en.wikipedia.org/wiki/HTTP_Live_Streaming
* http://gonze.com/playlists/playlist-format-survey.html

Project maintainers
--------------------

See `AUTHORS` file.

Alternatives
-------------

On the project start in 2013 there was no any other libs in Go for m3u8. Later the alternatives came. Some of them may be more fit current standards.

* https://github.com/hr8/rosso

Drop a link in issue if you know other projects.

FYI M3U8 parsing/generation in other languages
------------------------------------------

* https://github.com/globocom/m3u8 in Python
* https://github.com/zencoder/m3uzi in Ruby
* https://github.com/Jeanvf/M3U8Paser in Objective C
* https://github.com/tedconf/node-m3u8 in Javascript
* http://sourceforge.net/projects/m3u8parser/ in Java
* https://github.com/karlll/erlm3u8 in Erlang
