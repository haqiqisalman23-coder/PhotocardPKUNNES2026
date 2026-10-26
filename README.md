// 1. JALANKAN FUNGSI INI 1x DI EDITOR AGAR MUNCUL POP-UP IZIN GOOGLE DRIVE:
function beriIzinAksesDrive() {
  DriveApp.getRootFolder();
  Logger.log("Izin DriveApp berhasil diberikan!");
}

// 2. FUNGSI UNTUK MENERIMA DAN MENYIMPAN FOTO DARI WEBSITE
function doPost(e) {
  try {
    var data = JSON.parse(e.postData.contents);
    var targetFolderId = "1yXSfxRKoqjbr2Y5doG_57WtZxsxvQIss";
    var folder;

    try {
      folder = DriveApp.getFolderById(targetFolderId);
    } catch (errId) {
      var folderIter = DriveApp.getFoldersByName("Gelar_Karya_Mahasiswa_2026");
      if (folderIter.hasNext()) {
        folder = folderIter.next();
      } else {
        folder = DriveApp.createFolder("Gelar_Karya_Mahasiswa_2026");
      }
    }

    var decoded = Utilities.base64Decode(data.base64Data);
    var blob = Utilities.newBlob(decoded, data.mimeType || "image/png", data.fileName || ("Photocard_" + Date.now() + ".png"));
    var file = folder.createFile(blob);

    try {
      file.setSharing(DriveApp.Access.ANYONE_WITH_LINK, DriveApp.Permission.VIEW);
    } catch (eShare) {}

    return ContentService.createTextOutput(JSON.stringify({
      status: "success",
      fileId: file.getId(),
      fileUrl: file.getUrl(),
      fileName: file.getName(),
      folderName: folder.getName()
    })).setMimeType(ContentService.MimeType.JSON);

  } catch (err) {
    return ContentService.createTextOutput(JSON.stringify({
      status: "error",
      message: err.toString()
    })).setMimeType(ContentService.MimeType.JSON);
  }
}

// 3. FUNGSI UNTUK TES KONEKSI
function doGet(e) {
  return ContentService.createTextOutput(JSON.stringify({
    status: "ready",
    message: "Google Apps Script Photocard Studio siap digunakan!"
  })).setMimeType(ContentService.MimeType.JSON);
}
