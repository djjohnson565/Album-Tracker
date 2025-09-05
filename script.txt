


/*
UNCHECK THE DAILY ALBUMS
*/

function resetCheckboxes() {
  var sheet = SpreadsheetApp.getActiveSpreadsheet().getActiveSheet();
  var range = sheet.getRange('I2:I5');
  var values = range.getValues();
  
  for (var i = 0; i < values.length; i++) {
    values[i][0] = false;
  }
  
  range.setValues(values);
}


/*
CLEAR ALL LINES
*/

function clearDividers() {
  var sheet = SpreadsheetApp.getActiveSpreadsheet().getActiveSheet();
  var column = sheet.getRange("C2:C"); // Date range in column C starting from C2
  console.log("About to Clear...");
  for (var i = 2; i <= 100; i++) { //I doubt there will be >  new entries
    sheet.getRange(i, 1, 1, 4).setBorder(false, false, false, false, false, false);
  }
  console.log("Cleared!");
}

/*
UPDATE COLUMN OF COMPLETED DISCOGRAPHIES
*/

function updateCompletedArtists() {
  var sheet = SpreadsheetApp.getActiveSpreadsheet().getActiveSheet();
  var data = sheet.getRange("A2:D" + sheet.getLastRow()).getValues(); 
  var artists = {}; 
  var completedArtists = []; 
  var today = new Date(); 
  var totalTitles = sheet.getRange("E5").getValue();

  for (var i = 0; i < data.length; i++) {
    var artist = data[i][1];
    var listened = data[i][3]; 
    var releaseDate = new Date(data[i][2]);

    if (releaseDate <= today) {
      if (!artists[artist]) {
        artists[artist] = { total: 0, listened: 0 };
      }
      artists[artist].total++;
      if (listened === true) {
        artists[artist].listened++;
      }
    }
  }

  for (var artist in artists) {
    if (artists[artist].total === artists[artist].listened) {
      completedArtists.push({ name: artist, count: artists[artist].total });
    }
  }

  completedArtists.sort(function(a, b) {
    var nameA = a.name.replace(/^The\s+/i, '');
    var nameB = b.name.replace(/^The\s+/i, '');
    return nameA.localeCompare(nameB);
  });

  var completedColumnRange = sheet.getRange("K2:K");
  completedColumnRange.clearContent();

  for (var j = 0; j < completedArtists.length; j++) {
    var artistName = completedArtists[j].name;
    var titleCount = completedArtists[j].count;
    var completionPercentage = (titleCount / totalTitles) * 100;
    completionPercentage = Math.round(completionPercentage * 100) / 100;
    sheet.getRange(j + 2, 11).setValue(artistName + " (" + titleCount + ", %" + completionPercentage + ")");
  }
}


/*
SHOW ALL ARTISTS
*/

function showArtistCount() {
  var sheet = SpreadsheetApp.getActiveSpreadsheet().getActiveSheet();
  
  var data = sheet.getRange('B2:D' + sheet.getLastRow()).getValues();
  
  var artistCounts = {};
  
  for (var i = 0; i < data.length; i++) {
    var artist = data[i][0];
    var listened = data[i][2];

    if (artist) {
      if (!artistCounts[artist]) {
        artistCounts[artist] = { total: 0, completed: 0 };
      }
      artistCounts[artist].total++;
      
      if (listened === true) {
        artistCounts[artist].completed++;
      }
    }
  }
  
  function sortArtist(a, b) {
    var artistA = a.startsWith("The ") ? a.slice(4) : a;
    var artistB = b.startsWith("The ") ? b.slice(4) : b;
    
    return artistA.localeCompare(artistB);
  }

  var sortedArtists = Object.keys(artistCounts).sort(sortArtist);
  
  var message = '';
  sortedArtists.forEach(function(artist) {
    var totalAlbums = artistCounts[artist].total;
    var completedAlbums = artistCounts[artist].completed;
    message += artist + ' (' + completedAlbums + '/' + totalAlbums + ' completed)\n';
  });
  
  SpreadsheetApp.getUi().alert('Artist Completion Counts', message, SpreadsheetApp.getUi().ButtonSet.OK);
}




/*
ADD A DIVIDER BETWEEN CURRENT ALBUMS AND FUTURE RELEASES
*/


function addDivider() {
  var sheet = SpreadsheetApp.getActiveSpreadsheet().getActiveSheet();
  var today = new Date();
  var futureDateRow = -1;
  var earliestFutureDate = null;
  var allRows = sheet.getLastRow();

  for (var i = 2; i <= allRows; i++) {
    var cellValue = new Date(sheet.getRange(i, 3).getValue());
    if (cellValue >= today) {
        sheet.getRange(i, 1, 1, 4).setBorder(false, false, false, false, false, false);
    }else {
      break;
    }
  }

  for (var i = 2; i <= allRows; i++) {
    var cellValue = new Date(sheet.getRange(i, 3).getValue());
    if (cellValue >= today) {
      if (!earliestFutureDate || cellValue <= earliestFutureDate) {
        earliestFutureDate = cellValue;
        futureDateRow = i;
      }
    }else if(cellValue <= today) {
      break;
    }
  }

  if (futureDateRow != -1) {
    sheet.getRange(futureDateRow, 1, 1, 4).setBorder(false, false, true, false, false, false, "black", SpreadsheetApp.BorderStyle.SOLID_THICK);
  }
}


/*
SORTING THE SHEET
*/


//BY DATE (DEFAULT) AND TO FIX ALL FORMATTING
function sortData() {

  var sheet = SpreadsheetApp.getActiveSpreadsheet().getActiveSheet();
  var importantRange = sheet.getRange("E2:J22");
  var importantFormulas = importantRange.getFormulas();
  var dataRange = sheet.getRange("A2:D" + sheet.getLastRow());

  var daily = sheet.getRange("I2").getValue();
  var catchup = sheet.getRange("I3").getValue();
  var old = sheet.getRange("I4").getValue();
  var rotation = sheet.getRange("I5").getValue();

  dataRange.sort({column: 2, ascending: true});
  importantRange.setFormulas(importantFormulas);

  dataRange.sort({column: 3, ascending: false});
  importantRange.setFormulas(importantFormulas);
  sheet.getRange("E4").setValue("----------------------");
  sheet.getRange("H2").setValue("Daily");
  sheet.getRange("H3").setValue("Catchup");
  sheet.getRange("H4").setValue("Old");
  sheet.getRange("H5").setValue("Rotation");
  sheet.getRange("E4").setHorizontalAlignment("center");
  sheet.getRange("H2:H5").setHorizontalAlignment("center");

  sheet.getRange("I2").setValue(daily);
  sheet.getRange("I3").setValue(catchup);
  sheet.getRange("I4").setValue(old);
  sheet.getRange("I5").setValue(rotation);

  sheet.getRange("A1").setValue("Album");
  sheet.getRange("B1").setValue("Artist");
  sheet.getRange("C1").setValue("Release Date");
  sheet.getRange("D1").setValue("Listened");
  sheet.getRange("E1").setValue("Count");
  sheet.getRange("F1").setValue("Percent");
  sheet.getRange("G1").setValue("Album Anniversaries Today");
  sheet.getRange("G16").setValue("Albums Behind 2025 - Present");
  sheet.getRange("H1").setValue("Album");
  sheet.getRange("I1").setValue("Played");
  sheet.getRange("J1").setValue("Random Album");
  sheet.getRange("K1").setValue("Completed");

  sheet.getRange("E1").setHorizontalAlignment("center");
  sheet.getRange("F1").setHorizontalAlignment("center");
  sheet.getRange("G1").setHorizontalAlignment("center");
  sheet.getRange("G16").setHorizontalAlignment("center");
  sheet.getRange("H1").setHorizontalAlignment("center");
  sheet.getRange("I1").setHorizontalAlignment("center");
  sheet.getRange("J1").setHorizontalAlignment("center");
  sheet.getRange("K1").setHorizontalAlignment("center");

  addDivider();
  updateCompletedArtists();
  countFalses();
}


//BY ALBUM
function sortAlbum() {
  var sheet = SpreadsheetApp.getActiveSpreadsheet().getActiveSheet();
  var importantRange = sheet.getRange("E2:J5");
  var importantFormulas = importantRange.getFormulas();
  var dataRange = sheet.getRange("A2:D" + sheet.getLastRow());
  var allRows = sheet.getLastRow();

  var daily = sheet.getRange("I2").getValue();
  var catchup = sheet.getRange("I3").getValue();
  var old = sheet.getRange("I4").getValue();
  var rotation = sheet.getRange("I5").getValue();

  dataRange.sort({column: 1, ascending: true});
  importantRange.setFormulas(importantFormulas);
  sheet.getRange("E4").setValue("----------------------");
  sheet.getRange("H2").setValue("Daily");
  sheet.getRange("H3").setValue("Catchup");
  sheet.getRange("H4").setValue("Old");
  sheet.getRange("H5").setValue("Rotation");
  sheet.getRange("E4").setHorizontalAlignment("center");
  sheet.getRange("H2:H5").setHorizontalAlignment("center");

  sheet.getRange("I2").setValue(daily);
  sheet.getRange("I3").setValue(catchup);
  sheet.getRange("I4").setValue(old);
  sheet.getRange("I5").setValue(rotation);

  for (var i = 2; i <= allRows; i++) {
    sheet.getRange(i, 1, 1, 4).setBorder(false, false, false, false, false, false); // Only columns A-D
  }
  updateCompletedArtists()
}


//BY ARTIST
function sortArtist() {
  var sheet = SpreadsheetApp.getActiveSpreadsheet().getActiveSheet();
  var importantRange = sheet.getRange("E2:J5");
  var importantFormulas = importantRange.getFormulas();
  var dataRange = sheet.getRange("A2:D" + sheet.getLastRow());
  var allRows = sheet.getLastRow();

  var daily = sheet.getRange("I2").getValue();
  var catchup = sheet.getRange("I3").getValue();
  var old = sheet.getRange("I4").getValue();
  var rotation = sheet.getRange("I5").getValue();

  dataRange.sort({column: 2, ascending: true});
  importantRange.setFormulas(importantFormulas);
  sheet.getRange("E4").setValue("----------------------");
  sheet.getRange("H2").setValue("Daily");
  sheet.getRange("H3").setValue("Catchup");
  sheet.getRange("H4").setValue("Old");
  sheet.getRange("H5").setValue("Rotation");
  sheet.getRange("E4").setHorizontalAlignment("center");
  sheet.getRange("H2:H5").setHorizontalAlignment("center");

  sheet.getRange("I2").setValue(daily);
  sheet.getRange("I3").setValue(catchup);
  sheet.getRange("I4").setValue(old);
  sheet.getRange("I5").setValue(rotation);

  for (var i = 2; i <= allRows; i++) {
    sheet.getRange(i, 1, 1, 4).setBorder(false, false, false, false, false, false); // Only columns A-D
  }
  updateCompletedArtists()
}


/*
GETTING THE TRACKLISTS
*/


function showTracklistPopup() {
  var sheet = SpreadsheetApp.getActiveSpreadsheet().getActiveSheet();
  var activeCell = sheet.getActiveCell();

  var albumRow = activeCell.getRow();

  var album = sheet.getRange(albumRow, 1).getValue();
  var artist = sheet.getRange(albumRow, 2).getValue();
  var albumYear = sheet.getRange(albumRow, 3).getValue(); // Get the album year from column C

  if (!album || !artist || !albumYear) {
    SpreadsheetApp.getUi().alert('Please select a row with album, artist name, and album year.');
    return;
  }

  var mbid = searchAlbum(artist, album, albumYear);

  if (mbid) {
    var tracklist = fetchTracklist(mbid);
    
    if (!tracklist || tracklist.trim() === "") {
      SpreadsheetApp.getUi().alert('Tracklist could not be fetched.');
      return;
    }

    displayTracklistPopup(album, artist, tracklist);
  } else {
    SpreadsheetApp.getUi().alert('Album not found.');
  }
}

function displayTracklistPopup(album, artist, tracklist) {
  var tracklistLines = tracklist.split("\n");
  var formattedTracklist = '';

  for (var i = 0; i < tracklistLines.length; i++) {
    var track = tracklistLines[i].trim();

    if (track) {
      var trackNumber = (i + 1);
      formattedTracklist += trackNumber + ". " + track + "<br>";
    }
  }

  var htmlContent = `
    <html>
      <head>
        <style>
          body {
            font-family: Arial, sans-serif;
            padding: 10px;
          }
          h2 {
            text-align: center;
          }
          .tracklist {
            margin-top: 20px;
          }
        </style>
      </head>
      <body>
        <h2>${album}<br>${artist}</h2>
        <div class="tracklist">
          ${formattedTracklist}
        </div>
      </body>
    </html>
  `;
  
  var htmlOutput = HtmlService.createHtmlOutput(htmlContent)
    .setWidth(400)
    .setHeight(400);
  SpreadsheetApp.getUi().showModalDialog(htmlOutput, 'Tracklist');
}

function searchAlbum(artist, album, albumYear) {
  var baseUrl = "https://musicbrainz.org/ws/2/release";
  var query = "artist:" + encodeURIComponent(artist) + " album:" + encodeURIComponent(album);
  
  var url = baseUrl + "?query=" + query + "&fmt=json";
  
  var response = UrlFetchApp.fetch(url);
  var json = JSON.parse(response.getContentText());

  Logger.log("MusicBrainz API Response: " + JSON.stringify(json));

  if (json.releases && json.releases.length > 0) {
    var closestMatch = null;
    var closestMatchScore = 0;

    json.releases.forEach(function(release) {
      var matchScore = 0;

      if (release.title && release.title.toLowerCase().includes(album.toLowerCase())) {
        matchScore += 50; // add points for title match

        if (release.date) {
          var releaseYear = new Date(release.date).getFullYear();
          if (albumYear && releaseYear === albumYear) {
            matchScore += 30; // add points for a year match
          }
        }
      }

      if (matchScore > closestMatchScore) {
        closestMatch = release;
        closestMatchScore = matchScore;
      }
    });

    return closestMatch ? closestMatch.id : null;
  } else {
    return null;
  }
}

function fetchTracklist(mbid) {
  var baseUrl = "https://musicbrainz.org/ws/2/release/";
  
  var url = baseUrl + mbid + "?inc=recordings&fmt=json";
  
  var response = UrlFetchApp.fetch(url);
  var json = JSON.parse(response.getContentText());

  Logger.log("Tracklist API Response: " + JSON.stringify(json));

  var tracklist = '';
  if (json.media && json.media.length > 0) {
    var tracks = json.media[0].tracks; 
    if (tracks && tracks.length > 0) {
      tracks.forEach(function(track) {
        if (track.recording && track.recording.title && !track.recording.disambiguation) {
          var trackTitle = track.title;
          tracklist += trackTitle + "\n";
        }
      });
    } else {
      tracklist = "No tracklist found for the album.";
    }
  } else {
    tracklist = "No tracklist found for the album.";
  }

  return tracklist;
}

/*
SORT TRACKER ON SHEET 2
*/

function sortTracker() {
  var sheet = SpreadsheetApp.getActiveSpreadsheet().getSheetByName("Sheet2");
  var col = 2;
  var range = sheet.getRange(2, col, sheet.getLastRow() - 1, 1);
  range.sort({column: col, ascending: true});
  col = 3;
  range = sheet.getRange(2, col, sheet.getLastRow() - 1, 1);
  range.sort({column: col, ascending: true});
  col = 4;
  range = sheet.getRange(2, col, sheet.getLastRow() - 1, 1);
  range.sort({column: col, ascending: true});
}


function sortBetter() {
  var sheet = SpreadsheetApp.getActiveSpreadsheet().getSheetByName("Sheet2");
  var data = sheet.getRange('B2:B' + sheet.getLastRow()).getValues();
  
  var artistCounts = {};
  
  for (var i = 0; i < data.length; i++) {
    var artist = data[i][0];
    var listened = data[i][2];

    if (artist) {
      if (!artistCounts[artist]) {
        artistCounts[artist] = { total: 0, completed: 0 };
      }
      artistCounts[artist].total++;
      
      if (listened === true) {
        artistCounts[artist].completed++;
      }
    }
  }
  
  function sortArtist(a, b) {
    var artistA = a.startsWith("The ") ? a.slice(4) : a;
    var artistB = b.startsWith("The ") ? b.slice(4) : b;
    
    return artistA.localeCompare(artistB);
  }

  var sortedArtists = Object.keys(artistCounts).sort(sortArtist);
  
  console.log(sortedArtists);
  var completedColumnRange = sheet.getRange("B2:B");
  completedColumnRange.clearContent();

  for (var j = 0; j < sortedArtists.length; j++) {
    var artistName = sortedArtists[j];
    sheet.getRange(j + 2, 2).setValue(artistName);
  }

  var data = sheet.getRange('C2:C' + sheet.getLastRow()).getValues();
  
  var artistCounts = {};
  
  for (var i = 0; i < data.length; i++) {
    var artist = data[i][0];
    var listened = data[i][2];

    if (artist) {
      if (!artistCounts[artist]) {
        artistCounts[artist] = { total: 0, completed: 0 };
      }
      artistCounts[artist].total++;
      
      if (listened === true) {
        artistCounts[artist].completed++;
      }
    }
  }
  
  function sortArtist(a, b) {
    var artistA = a.startsWith("The ") ? a.slice(4) : a;
    var artistB = b.startsWith("The ") ? b.slice(4) : b;
    
    return artistA.localeCompare(artistB);
  }

  var sortedArtists = Object.keys(artistCounts).sort(sortArtist);
  
  console.log(sortedArtists);
  var completedColumnRange = sheet.getRange("C2:C");
  completedColumnRange.clearContent();

  for (var j = 0; j < sortedArtists.length; j++) {
    var artistName = sortedArtists[j];
    sheet.getRange(j + 2, 3).setValue(artistName);
  }

  var data = sheet.getRange('D2:D' + sheet.getLastRow()).getValues();
  
  var artistCounts = {};
  
  for (var i = 0; i < data.length; i++) {
    var artist = data[i][0];
    var listened = data[i][2];

    if (artist) {
      if (!artistCounts[artist]) {
        artistCounts[artist] = { total: 0, completed: 0 };
      }
      artistCounts[artist].total++;
      
      if (listened === true) {
        artistCounts[artist].completed++;
      }
    }
  }
  
  function sortArtist(a, b) {
    var artistA = a.startsWith("The ") ? a.slice(4) : a;
    var artistB = b.startsWith("The ") ? b.slice(4) : b;
    
    return artistA.localeCompare(artistB);
  }

  var sortedArtists = Object.keys(artistCounts).sort(sortArtist);
  
  console.log(sortedArtists);
  var completedColumnRange = sheet.getRange("D2:D");
  completedColumnRange.clearContent();

  for (var j = 0; j < sortedArtists.length; j++) {
    var artistName = sortedArtists[j];
    sheet.getRange(j + 2, 4).setValue(artistName);
  }
}

/*
CHECK TO SEE HOW FAR BEHIND FROM 1/1/2025
*/

function countFalses() {
  var sheet = SpreadsheetApp.getActiveSpreadsheet().getActiveSheet();
  var today = new Date();
  var countFalses = 0;
  var startDate = new Date('1/1/2025');
  
  for (var i = 2; i <= 1000; i++) {
    var dateValue = new Date(sheet.getRange(i, 3).getValue()); 
    var statusValue = sheet.getRange(i, 4).getValue(); 
    
    if (dateValue >= startDate && dateValue <= today && statusValue === false) {
      countFalses++;
    }
  }

  //Logger.log("Count of Falses: " + countFalses);
  sheet.getRange("G16").setValue("Albums Behind 2025 - Present");
  sheet.getRange("G16").setHorizontalAlignment("center");
  sheet.getRange("G18").setValue(countFalses);
  sheet.getRange("G18").setHorizontalAlignment("center");
}

/*
Execute at 12AM
*/

function customDailyReset() {
  resetCheckboxes();
  console.log("Reset Checkboxes");
  clearDividers();
  console.log("Cleared Dividers");
  sortData();
  console.log("Sorted Data");
}


