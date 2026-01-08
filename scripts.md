<!-- var sheetId = '1tGkb36fnSNMLkodPvsqfax8Ik_xjPw3rwiPMtIoxoaI'; // Thay ID Sheet của bạn vào đây

function doGet(e) {
  return handleRequest(e);
}

function doPost(e) {
  return handleRequest(e);
}

function handleRequest(e) {
  var lock = LockService.getScriptLock();
  lock.tryLock(10000); // Khóa 10s để tránh 2 người cùng giật 1 giải cuối cùng

  try {
    var action = e.parameter.action;
    var ss = SpreadsheetApp.openById(sheetId);

    // --- CHECK CODE VÀ QUAY SỐ TẠI SERVER ---
    if (action == 'check_code') {
      var code = e.parameter.code;
      var sheet = ss.getSheetByName('Codes');
      var data = sheet.getDataRange().getValues();
      var rowIndex = -1;

      // 1. Tìm dòng chứa code
      for (var i = 1; i < data.length; i++) {
        if (data[i][0] == code) {
          // Nếu đã dùng rồi thì báo lỗi
          if (data[i][1] == 'Used') {
            return responseJSON({ status: 'error', message: 'Mã này đã được sử dụng!' });
          }
          rowIndex = i + 1; // Số dòng trong Sheet (index + 1)
          break;
        }
      }

      if (rowIndex == -1) {
        return responseJSON({ status: 'error', message: 'Mã code không tồn tại!' });
      }

      // 2. Tính toán kho giải thưởng còn lại
      // Tổng giới hạn: 10 vé 1tr8, 20 vé 1tr6, 20 vé 1tr4
      var limits = {
        "Voucher 1.800.000đ": 10,
        "Voucher 1.600.000đ": 20,
        "Voucher 1.400.000đ": 20
      };

      var usedCount = {
        "Voucher 1.800.000đ": 0,
        "Voucher 1.600.000đ": 0,
        "Voucher 1.400.000đ": 0
      };

      // Đếm số giải đã trao trong cột C (index 2)
      for (var i = 1; i < data.length; i++) {
        var p = data[i][2]; // Cột C
        if (p && usedCount.hasOwnProperty(p)) {
          usedCount[p]++;
        }
      }

      // Tạo "hộp" phiếu thăm dựa trên số lượng còn lại
      var pool = [];
      for (var key in limits) {
        var remaining = limits[key] - usedCount[key];
        for (var k = 0; k < remaining; k++) {
          pool.push(key);
        }
      }

      // Xử lý trường hợp hết sạch giải (hiếm khi xảy ra nếu tính đúng 50 code)
      if (pool.length === 0) {
        return responseJSON({ status: 'error', message: 'Đã hết giải thưởng!' });
      }

      // 3. Bốc thăm ngẫu nhiên từ pool
      var randomIndex = Math.floor(Math.random() * pool.length);
      var selectedPrize = pool[randomIndex];

      // 4. Lưu ngay kết quả vào Sheet (Đánh dấu đã dùng và giải trúng)
      sheet.getRange(rowIndex, 2).setValue('Used');         // Cột B
      sheet.getRange(rowIndex, 3).setValue(selectedPrize);  // Cột C (Lưu hạng vé)

      // Trả về giải thưởng cho Frontend quay
      return responseJSON({ status: 'success', prize: selectedPrize });
    }

    // --- LƯU THÔNG TIN NGƯỜI TRÚNG ---
    if (action == 'save_winner') {
      var sheet = ss.getSheetByName('Winners');
      sheet.appendRow([
        new Date(),
        e.parameter.code,
        e.parameter.name,
        e.parameter.email,
        e.parameter.phone,
        e.parameter.prize // Giải thưởng được frontend gửi lên lại để confirm
      ]);
      return responseJSON({ status: 'success', message: 'Đã lưu thông tin' });
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
} -->
