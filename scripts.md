<!-- var sheetId = 'YOUR_SPREADSHEET_ID_HERE'; // Thay ID Sheet của bạn vào đây

function doGet(e) {
  return handleRequest(e);
}

function doPost(e) {
  return handleRequest(e);
}

function handleRequest(e) {
  var lock = LockService.getScriptLock();
  lock.tryLock(10000);

  try {
    var action = e.parameter.action;
    var ss = SpreadsheetApp.openById(sheetId);

    // 1. Kiểm tra Code (CHỈ KIỂM TRA, KHÔNG ĐÁNH DẤU 'Used')
    if (action == 'check_code') {
      var code = e.parameter.code;
      var sheet = ss.getSheetByName('Codes');
      var data = sheet.getDataRange().getValues();
      var rowIndex = -1;

      for (var i = 1; i < data.length; i++) {
        if (String(data[i][0]) == String(code)) {
          if (String(data[i][1]).toLowerCase() == 'used') {
            return responseJSON({ status: 'error', message: 'Mã này đã được sử dụng!' });
          }
          rowIndex = i + 1;
          break;
        }
      }

      if (rowIndex == -1) {
        return responseJSON({ status: 'error', message: 'Mã code không tồn tại!' });
      }

      // Không đánh dấu 'Used' ở bước kiểm tra. Chỉ trả về thành công.
      return responseJSON({ status: 'success', message: 'Code hợp lệ' });
    }

    // 2. Lưu thông tin người trúng và ĐÁNH DẤU 'Used' (kiểm tra lại tại thời điểm lưu)
    if (action == 'save_winner') {
      var code = e.parameter.code;
      var sheetCodes = ss.getSheetByName('Codes');
      var data = sheetCodes.getDataRange().getValues();
      var rowIndex = -1;

      for (var i = 1; i < data.length; i++) {
        if (String(data[i][0]) == String(code)) {
          rowIndex = i + 1;
          break;
        }
      }

      if (rowIndex == -1) {
        return responseJSON({ status: 'error', message: 'Mã code không tồn tại!' });
      }

      var statusCell = sheetCodes.getRange(rowIndex, 2);
      var status = String(statusCell.getValue());
      if (status.toLowerCase() == 'used') {
        return responseJSON({ status: 'error', message: 'Mã này đã được sử dụng!' });
      }

      // Đánh dấu 'Used' trước khi lưu Winners để tránh race condition
      statusCell.setValue('Used');

      var sheetW = ss.getSheetByName('Winners');
      sheetW.appendRow([
        new Date(),
        code,
        e.parameter.name,
        e.parameter.email,
        e.parameter.phone,
        e.parameter.prize
      ]);

      return responseJSON({ status: 'success', message: 'Đã lưu thông tin' });
    }

    // 3. Trả về tổng số mã và số đã dùng (dùng cho client status)
    if (action == 'count_codes') {
      var sheetCodes = ss.getSheetByName('Codes');
      var vals = sheetCodes.getDataRange().getValues();
      var total = Math.max(0, vals.length - 1);
      var used = 0;
      for (var i = 1; i < vals.length; i++) {
        if (String(vals[i][1]).toLowerCase() == 'used') used++;
      }
      return responseJSON({ status: 'success', used: used, total: total });
    }

  } catch (e) {
    return responseJSON({ status: 'error', message: e.toString() });
  } finally {
    lock.releaseLock();
  }
}

function responseJSON(data) {
  return ContentService
    .createTextOutput(JSON.stringify(data))
    .setMimeType(ContentService.MimeType.JSON);
}
 -->
