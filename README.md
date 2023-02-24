# Python-Hotline
hi there, i appreciate all the help
# This is a sample Python script.

# Press Shift+F10 to execute it or replace it with your code.
# Press Double Shift to search everywhere for classes, files, tool windows, actions, and settings.


# Press the green button in the gutter to run the script.




# See PyCharm help at https://www.jetbrains.com/help/pycharm/
import sys
import mysql.connector
from PyQt5.QtWidgets import QApplication, QTableWidgetItem, QCompleter, QLineEdit
from PyQt5.QtWidgets import QMainWindow, QMessageBox
from PyQt5.QtGui import QStandardItem, QStandardItemModel
from hotline import Ui_MainWindow
from openpyxl import workbook, load_workbook
import numpy as np

db = mysql.connector.connect(user='root', password='Minbaby1107',
                             host='hotline-database.co7siclmjerp.ap-northeast-1.rds.amazonaws.com', database='hotline_database')
cur = db.cursor()
wb = load_workbook('hotline.xlsx')


class MainWindow:
    def __init__(self):
        self.manhinhcode = QMainWindow()
        self.manhinhui = Ui_MainWindow()
        self.manhinhui.setupUi(self.manhinhcode)
        self.manhinhui.tabWidget.setCurrentWidget(self.manhinhui.tab_2)
        # self.manhinhui.But_loadCV.clicked.connect(self.capnhat_congviec)
        # self.mode1 = QStandardItemModel()
        # completer = QCompleter(self.mode1, self)
        # self.manhinhui.lineEdit.setCompleter(completer)
        self.manhinhui.table_congviec.addAction(self.shows())
        self.manhinhui.table_vitri.addAction(self.show_dinhmuc())
        self.manhinhui.But_chonHM.clicked.connect(self.show2)
        self.manhinhui.But_tinhtoan.clicked.connect(self.tinhtoan)
        self.manhinhui.lineEdit.editingFinished.connect(self.timkiem)
        self.manhinhui.Button_them_2.clicked.connect(self.them)
        self.manhinhui.line_vitri.editingFinished.connect(self.tim_vitri)

        xuonghang = self.manhinhui.tableWidget.horizontalHeaderItem(5)
        xuonghang.setText("Đơn giá \n nhân công")
        xuonghang2 = self.manhinhui.tableWidget.horizontalHeaderItem(6)
        xuonghang2.setText("Đơn giá \n MTC")
        xuonghang3 = self.manhinhui.tableWidget.horizontalHeaderItem(13)
        xuonghang3.setText("Giá dự toán \n trước thuế")
    def tim_vitri(self):
        try:
            self.manhinhui.table_vitri.clear()
            a = self.manhinhui.line_vitri.text()
            timkiem_vitri = 'SELECT * FROM vitri WHERE vitri LIKE "%' + a + '%"'
            cur.execute(timkiem_vitri)
            result_vitri = cur.fetchall()
            print(result_vitri)
            a = 0
            for row in result_vitri:
                a = len(row)
            self.manhinhui.table_vitri.setRowCount(len(result_vitri))
            self.manhinhui.table_vitri.setColumnCount(a)
            for row_number, row_data in enumerate(result_vitri):
                for column_number, data in enumerate(row_data):
                    self.manhinhui.table_vitri.setItem(row_number, column_number, QTableWidgetItem(str(data)))
        except:
            print("Không thấy công việc")
            QMessageBox.about(self.manhinhui.table_vitri, 'Thông báo', 'Không tìm thấy vị trí')
    def timkiem(self):
        try:
            self.manhinhui.table_congviec.clear()
            a = self.manhinhui.lineEdit.text()
            timkiem_congviec = 'SELECT * FROM congviec WHERE TenCV LIKE "%' + a + '%"'
            cur.execute(timkiem_congviec)
            result_timkiem = cur.fetchall()
            print(result_timkiem)
            a = 0
            for row in result_timkiem:
                a = len(row)
            self.manhinhui.table_congviec.setRowCount(len(result_timkiem))
            self.manhinhui.table_congviec.setColumnCount(a)
            for row_number, row_data in enumerate(result_timkiem):
                for column_number, data in enumerate(row_data):
                    self.manhinhui.table_congviec.setItem(row_number, column_number, QTableWidgetItem(str(data)))
            print(int(self.manhinhui.table_congviec.item(0, 3).text()))
        except:
            print("Không thấy công việc")
            QMessageBox.about(self.manhinhui.table_congviec, 'Thông báo', 'Không tìm thấy công việc')
    def tinhtoan(self):
        c = self.manhinhui.tableWidget.rowCount()
        for i in range(1, c):
            a = int(self.manhinhui.tableWidget.item(i, 3).text())
            if a != 0:
                dongia_vatlieu = float(self.manhinhui.tableWidget.item(i, 4).text())
                dongia_nhancong = float(self.manhinhui.tableWidget.item(i, 5).text())
                dongia_MTC = float(self.manhinhui.tableWidget.item(i, 6).text())
                HS_tutinh = float(self.manhinhui.tableWidget.item(i, 14).text())
                HS_bosung = float(self.manhinhui.tableWidget.item(i, 15).text())
                thanhtien_vatlieu = float(a * dongia_vatlieu)
                thanhtien_nhancong = a * dongia_nhancong * HS_tutinh * HS_bosung
                thanhtien_MTC = a * dongia_MTC
                CP_tructiep = int(thanhtien_vatlieu + thanhtien_nhancong + thanhtien_MTC)
                CP_chung = int(0.35 * thanhtien_nhancong)
                TN_chiuthue = int(0.06 * (CP_chung + CP_tructiep))
                Dutoan_truocthue = int(CP_chung + CP_tructiep + TN_chiuthue)
                self.manhinhui.tableWidget.setItem(i, 7, QTableWidgetItem(str(thanhtien_vatlieu)))
                self.manhinhui.tableWidget.setItem(i, 8, QTableWidgetItem(str(thanhtien_nhancong)))
                self.manhinhui.tableWidget.setItem(i, 9, QTableWidgetItem(str(thanhtien_MTC)))
                self.manhinhui.tableWidget.setItem(i, 10, QTableWidgetItem(str(CP_tructiep)))
                self.manhinhui.tableWidget.setItem(i, 11, QTableWidgetItem(str(CP_chung)))
                self.manhinhui.tableWidget.setItem(i, 12, QTableWidgetItem(str(TN_chiuthue)))
                self.manhinhui.tableWidget.setItem(i, 13, QTableWidgetItem(str(Dutoan_truocthue)))
    def show2(self):
        self.manhinhui.tableWidget.setColumnCount(17)
        self.manhinhui.tableWidget.setRowCount(1)
        hangmuc = self.manhinhui.comboBox_2.currentText()
        for item in self.manhinhui.table_vitri.selectedItems():
            row_item = item.row()
            vitri = self.manhinhui.table_vitri.item(row_item, 0)
            self.manhinhui.tableWidget.setItem(0, 0, QTableWidgetItem(vitri))
        self.manhinhui.tableWidget.setItem(0, 1, QTableWidgetItem(hangmuc))
        self.manhinhui.tableWidget.setItem(0, 16, QTableWidgetItem(hangmuc))
    def them(self):
        c = self.manhinhui.tableWidget.rowCount()
        self.manhinhui.tableWidget.setRowCount(c+1)
        hangmuc = self.manhinhui.comboBox_2.currentText()
        for currentItem in self.manhinhui.table_congviec.selectedItems():
            a = currentItem.row()
            b = self.manhinhui.table_congviec.item(a, 0).text()
            b1 = self.manhinhui.table_congviec.item(a, 1).text()
            b2 = self.manhinhui.table_congviec.item(a, 2).text()
            b3 = self.manhinhui.table_congviec.item(a, 3).text()
            b4 = self.manhinhui.table_congviec.item(a, 4).text()
            b5 = self.manhinhui.table_congviec.item(a, 5).text()
            b6 = self.manhinhui.table_vitri.item(a, 6).text()
            print(b)
            self.manhinhui.tableWidget.setItem(c, 0, QTableWidgetItem(b))
            self.manhinhui.tableWidget.setItem(c, 1, QTableWidgetItem(b1))
            self.manhinhui.tableWidget.setItem(c, 2, QTableWidgetItem(b2))
            self.manhinhui.tableWidget.setItem(c, 3, QTableWidgetItem(str(0)))
            self.manhinhui.tableWidget.setItem(c, 4, QTableWidgetItem(b3))
            self.manhinhui.tableWidget.setItem(c, 5, QTableWidgetItem(b4))
            self.manhinhui.tableWidget.setItem(c, 6, QTableWidgetItem(b5))
            self.manhinhui.tableWidget.setItem(c, 14, QTableWidgetItem(b6))
            self.manhinhui.tableWidget.setItem(c, 15, QTableWidgetItem('1'))
            # self.manhinhui.tableWidget.hideColumn(4)
            # self.manhinhui.tableWidget.hideColumn(5)
            # self.manhinhui.tableWidget.hideColumn(6)
            # self.manhinhui.tableWidget.hideColumn(15)
    def load_db_vatlieu(self):
        excel_vatlieu = wb['VATLIEU']
        m = []
        for i in excel_vatlieu.values:
            m.append(i[:])  # lay het tru 2 so cuoi
        data = m[1:]  # lay het tru dong dau tien
        xoa_vatlieu = "TRUNCATE TABLE vatlieu"
        cur.execute(xoa_vatlieu)
        stmt = "INSERT INTO vatlieu (TT, Tenvatlieu, Donvi, Dongia) VALUES (%s, %s, %s, %s)"
        cur.executemany(stmt, data)
        db.commit()
    def capnhat(self):
        excel_congviec = wb['CONGVIEC']
        n = []
        for i in excel_congviec.values:
                n.append(i[:]) # lay het tru 2 so cuoi
        data = n[1:] # lay het tru dong dau tien
        xoa_conggviec = "TRUNCATE TABLE congviec"
        cur.execute(xoa_conggviec)
        chen = "INSERT INTO congviec (MaDM, TenCV, Donvi, Vatlieuchinh, Nhancong, Maythicong) VALUES (%s, %s, %s, %s, %s, %s)"
        cur.executemany(chen, data)
        db.commit()
    def show_dinhmuc(self):
        excel_vitri = wb['VITRI']
        n = []
        for i in excel_vitri.values:
                n.append(i[:]) # lay het tru 2 so cuoi
        data = n[1:] # lay het tru dong dau tien
        xoa_vitri = "TRUNCATE TABLE vitri"
        cur.execute(xoa_vitri)
        chen = "INSERT INTO vitri (vitri, tba, fco, lbs, rec, chungcot, hesochung) VALUES (%s, %s, %s, %s, %s, %s, %s)"
        cur.executemany(chen, data)
        db.commit()
        load_vitri = "SELECT * FROM vitri"
        cur.execute(load_vitri)
        result_vitri = cur.fetchall()
        self.manhinhui.table_vitri.clear()
        a = 0
        for row in result_vitri:
            a = len(row)
        self.manhinhui.table_vitri.setRowCount(len(result_vitri))
        self.manhinhui.table_vitri.setColumnCount(a)
        for row_number, row_data in enumerate(result_vitri):
            for column_number, data in enumerate(row_data):
                self.manhinhui.table_vitri.setItem(row_number, column_number, QTableWidgetItem(str(data)))
    def shows(self):
        load_congviec = "SELECT * FROM congviec"
        cur.execute(load_congviec)
        result_congviec = cur.fetchall()
        a = 0
        for row in result_congviec:
            a = len(row)
        self.manhinhui.table_congviec.setRowCount(len(result_congviec))
        self.manhinhui.table_congviec.setColumnCount(a)
        for row_number, row_data in enumerate(result_congviec):
            for column_number, data in enumerate(row_data):
                self.manhinhui.table_congviec.setItem(row_number, column_number, QTableWidgetItem(str(data)))






    # ws = wb["Hotline"]

    # ws['E8'] = 10
    # wb.save('hotline.xlsx')
    # a = ws['I8'].value
    # b = ws['E8'].value
    # print(b)
    # col = ws['e']
    # # print(col)
    # col_value = []
    # for i in col:
    #     col_value.append(i.value)
    # print(col_value)
    # table = ws1['c4':'f9']
    # # print(table)
    #
    # a0 = []
    # for i in table[0]:
    #     a0.append(i.value)
    #
    # a1 = []
    # for i in table[1]:
    #     a1.append(i.value)
    #
    # a2 = []
    # for i in table[2]:
    #     a2.append(i.value)
    #
    # a3 = []
    # for i in table[3]:
    #     a3.append(i.value)
    #
    # a4 = []
    # for i in table[4]:
    #     a4.append(i.value)
    #
    # b = ([a0, a1, a2, a3, a4])
    # A = np.array(b)
    # print(A)

    #         self.manhinhui.stackedWidget.setCurrentWidget(self.manhinhui.home)
    #         self.manhinhui.pushButton.clicked.connect(self.showpage)
    #         self.manhinhui.pushButton_2.clicked.connect(self.showpage_2)
    #         self.manhinhui.pushButton_3.clicked.connect(self.showpage_3)
    #         self.manhinhui.home_findbut.clicked.connect(self.copyhome)
    #
    #     def copyhome(self):
    #         copy = self.manhinhui.tenCT.toPlainText()
    #         self.manhinhui.label.setText(copy)
    def show(self):
        self.manhinhcode.show()


#     def showpage(self):
#             self.manhinhui.stackedWidget.setCurrentWidget(self.manhinhui.page)
#     def showpage_2(self):
#         self.manhinhui.stackedWidget.setCurrentWidget(self.manhinhui.page_2)
#     def showpage_3(self):
#         self.manhinhui.stackedWidget.setCurrentWidget(self.manhinhui.page_3)
#
#
if __name__ == "__main__":
    app = QApplication(sys.argv)
    manhinhcode = MainWindow()
    manhinhcode.show()
    sys.exit(app.exec())
