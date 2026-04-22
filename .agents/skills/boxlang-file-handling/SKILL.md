---
name: boxlang-file-handling
description: "Use this skill when reading, writing, copying, moving, or deleting files and directories in BoxLang: fileRead, fileWrite, fileCopy, fileMove, directoryList, directoryCreate, fileUpload, streaming large files, or processing CSV/JSON files from disk."
---

# BoxLang File Handling

## Overview

BoxLang provides comprehensive file-system operations for reading, writing, and
managing files and directories. Supports text, binary, and streaming access with
configurable encoding.

## Reading Files

```boxlang
// Read entire file as string
var content = fileRead( "/path/to/file.txt" )

// Read with explicit encoding
var content = fileRead( "/path/to/file.txt", "UTF-8" )

// Read as array of lines
var lines = fileReadLines( "/path/to/file.txt" )
lines.each( ( line ) -> println( line ) )

// Read binary file
var bytes = fileReadBinary( "/path/to/image.jpg" )
```

### Streaming Large Files

```boxlang
function processLargeFile( filePath ) {
    var file = fileOpen( filePath, "read" )
    try {
        while ( !fileIsEOF( file ) ) {
            processLine( fileReadLine( file ) )
        }
    } finally {
        fileClose( file )
    }
}
```

## Writing Files

```boxlang
// Write (create or replace)
fileWrite( "/path/to/file.txt", "Hello World" )

// Write with encoding
fileWrite( "/path/to/file.txt", content, "UTF-8" )

// Append to file
fileWrite( "/path/to/file.txt", "New line\n", "UTF-8", true )

// Write binary
fileWriteBinary( "/path/to/image.jpg", binaryData )
```

### Buffered Write for Large Content

```boxlang
function writeLargeFile( filePath, lines ) {
    var file = fileOpen( filePath, "write" )
    try {
        lines.each( ( line ) -> fileWriteLine( file, line ) )
    } finally {
        fileClose( file )
    }
}
```

## Copy, Move, Delete

```boxlang
// Copy
fileCopy( "/source/file.txt", "/dest/file.txt" )
fileCopy( "/source/file.txt", "/dest/file.txt", true )  // overwrite

// Move / rename
fileMove( "/old/path/file.txt", "/new/path/file.txt" )

// Delete (check first)
if ( fileExists( "/path/to/file.txt" ) ) {
    fileDelete( "/path/to/file.txt" )
}
```

## Directory Operations

```boxlang
// List files
var files = directoryList( "/path/to/dir" )

// List with filter and full query
var result = directoryList(
    path: "/path/to/dir",
    filter: "*.txt",
    recurse: true,
    listInfo: "query"
)

// Create (including nested)
if ( !directoryExists( "/path/to/dir" ) ) {
    directoryCreate( "/path/to/dir", createPath: true )
}

// Copy directory
directoryCopy( "/source/dir", "/dest/dir", recurse: true )

// Move / rename
directoryRename( "/old/path", "/new/path" )

// Delete recursively
if ( directoryExists( "/path/to/dir" ) ) {
    directoryDelete( "/path/to/dir", recurse: true )
}
```

## File Information and Paths

```boxlang
// File attributes
if ( fileExists( "/path/to/file.txt" ) ) {
    var info = getFileInfo( "/path/to/file.txt" )
    // info.name, info.size, info.lastModified, info.mode, info.type
}

// Path utilities
var absolute = expandPath( "/uploads/file.txt" )
var dir      = getDirectoryFromPath( "/path/to/file.txt" )  // /path/to/
var name     = getFileFromPath( "/path/to/file.txt" )       // file.txt
var ext      = listLast( name, "." )                        // txt
```

## File Upload

```boxlang
function uploadFile( fileField, destination ) {
    if ( !form.keyExists( fileField ) ) {
        return { success: false, message: "No file uploaded" }
    }

    try {
        if ( !directoryExists( destination ) ) {
            directoryCreate( destination, createPath: true )
        }

        var upload = fileUpload(
            destination: destination,
            fileField: fileField,
            nameConflict: "makeUnique",
            accept: "image/jpeg,image/png,image/gif"
        )

        return {
            success: true,
            serverFile: upload.serverFile,
            clientFile: upload.clientFile,
            fileSize: upload.fileSize
        }
    } catch ( any e ) {
        return { success: false, message: "Upload failed: #e.message#" }
    }
}
```

## JSON and CSV Helpers

```boxlang
// JSON file round-trip
function readJSON( filePath ) {
    return deserializeJSON( fileRead( filePath ) )
}

function writeJSON( filePath, data ) {
    fileWrite( filePath, serializeJSON( data ) )
}

// CSV reader
function readCSV( filePath ) {
    var lines   = fileReadLines( filePath )
    var headers = listToArray( lines[1], "," )
    var data    = []

    lines.each( ( line, i ) -> {
        if ( i == 1 ) return
        var cols = listToArray( line, "," )
        var row  = {}
        cols.each( ( val, j ) -> { row[ headers[j] ] = val } )
        data.append( row )
    } )

    return data
}
```

## Best Practices

- Always use `try/finally` to close open file handles
- Validate uploads: check MIME type and file size before saving
- Use `expandPath()` to produce absolute paths; never string-concatenate user input directly into paths
- For path traversal safety, sanitize file names with `getFileFromPath()` before building destination paths
- Specify encoding (`"UTF-8"`) explicitly when text content matters
- Clean up temporary files in a `finally` block

## Security Patterns

```boxlang
// ✅ Safe: sanitize upload file name
var safeName = getFileFromPath( form.fileName )
var dest     = expandPath( "/uploads" ) & "/" & safeName
fileRead( dest )

// ❌ Unsafe: direct user input in path
fileRead( "/uploads/#form.fileName#" )  // Path traversal risk
```

## Log Rotation

```boxlang
function rotateLogFile( logFile, maxBytes = 10485760 ) {
    if ( !fileExists( logFile ) ) return

    if ( getFileInfo( logFile ).size > maxBytes ) {
        var backup = logFile & "." & dateFormat( now(), "yyyymmdd_HHnnss" )
        fileMove( logFile, backup )
        fileWrite( logFile, "" )
    }
}
```