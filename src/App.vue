<template>
  <div id="app">
    <el-upload
      class="left-span"
      action=""
      :on-change="handleChange"
      :show-file-list="false"
      :auto-upload="false"
    >
      <el-button size="small" type="primary">点击上传</el-button>
    </el-upload>
    <div id="stars"></div>
    <div id="stars2"></div>
    <div id="stars3"></div>
    <el-container>
      <el-header style="margin-top: 40px">
        <span class="textS">扬州奕丞科技有限公司</span></el-header
      >
      <el-main>
        <el-row>
          <el-col :span="15" style="padding-right: 100px; margin-top: 13rem">
            <el-col :span="24">
              <el-col :span="9"
                ><div class="textS">奖品:</div>
                <div class="textS">获奖人:</div>
              </el-col>
              <el-col :span="15" style="text-align: center">
                <div class="textS" style="text-align: left">
                  {{ nowRowPrizeName }}
                </div>
                <input
                  v-model="luckMan"
                  style="
                    font-size: 80px;
                    width: 100%;
                    margin-top: 30px;
                    font-family: simsun;
                    font-size: 80px;
                    color: #ffd700;
                    text-shadow: 5px 4px 4px #c24d00;
                    font-weight: bold;
                    background-color: transparent;
                    color: #ffd700;
                    border: none;
                  "
                />
              </el-col>
            </el-col>
            <el-col :span="24">
              <el-button
                type="primary"
                @click="getLuck()"
                round
                class="glass-button"
                style="
                  margin-top: 9 rem;
                  width: 40%;
                  font-size: 60px;
                  font-family: simsun;
                  margin-top: 5rem;
                  background-color: transparent;
                  text-shadow: 5px 4px 4px #c24d00;
                  border-color: #ce5151;
                  color: #ffd700;
                "
                >{{ buttonText }}</el-button
              >
            </el-col>
          </el-col>
          <el-col :span="7" class="prize_k">
            <span
              style="
                color: #fff;
                top: 3rem;
                text-align: center;
                font-weight: bold;
                text-shadow: 4px 3px 3px #c24d00;
                font-size: 2rem;
                position: relative;
              "
              >中奖清单</span
            >
            <el-table
              :data="prize"
              @row-click="chosePrize"
              highlight-current-row
              style="
                color: rgb(252, 60, 60);
                text-shadow: rgb(194, 77, 0) 1px 1px 1px;
                font-weight: bold;
                font-size: 23px;
                font-family: simsun;
                line-height: 40px;
                position: relative;
                width: 80%;
                overflow-y: auto;
                margin: 0 auto;
              "
              :header-cell-style="{ 'text-align': 'center' }"
              height="500"
            >
              <el-table-column prop="prizeName" label="奖品" align="center">
              </el-table-column>
              <el-table-column prop="prizeUser" label="获奖人" align="center">
              </el-table-column>
            </el-table>
            <span
              style="
                color: rgb(252, 60, 60);
                top: 2.5rem;
                text-align: center;
                font-weight: bold;
                text-shadow: rgb(194, 77, 0) 1px 1px 1px;
                font-size: 1.7rem;
                position: relative;
              "
              >当前奖品:{{ nowRowPrizeName }}</span
            >
          </el-col>
        </el-row>
      </el-main>
    </el-container>
  </div>
</template>

<script>
import XLSX from "xlsx";
export default {
  data() {
    return {
      luckMan: "",
      prize: [
        { prizeName: "苹果", prizeUser: null },
        { prizeName: "栗子", prizeUser: null },
        { prizeName: "橘子", prizeUser: null },
      ],
      allUser: ["张三", "李四", "王五"],
      vaildUser: ["张三", "李四", "王五"],
      timer: null,
      isRunning: false,
      buttonText: "开始抽奖",
      nowRow: null,
      nowRowPrizeName: "",
    };
  },
  created() {},
  methods: {
    // 选择奖品
    chosePrize(row) {
      this.nowRow = row;
      this.nowRowPrizeName = this.nowRow.prizeName;
      console.log(row.style);
      if (this.nowRow.prizeUser == null) {
        this.buttonText = "开始抽奖";
      } else {
        this.buttonText = "重新抽奖";
      }
    },
    // 开始抽奖
    getLuck() {
      if (!this.isRunning) {
        if (this.nowRow == null) {
          this.$message({
            type: "warning",
            message: "请先选择需要奖品",
          });
          return;
        }
        this.isRunning = true;
        this.buttonText = "停止";
        this.startLuck();
      } else {
        this.isRunning = false;
        this.stopLuck();
        this.buttonText = "开始抽奖";
        console.log(this.luckMan + "抽中" + this.nowRowPrizeName)
        // this.$alert(
        //   "恭喜:" + this.luckMan + "抽中" + this.nowRowPrizeName + "!!!",
        //   "恭喜中奖!",
        //   {
        //     confirmButtonText: "确定",
        //   }
        // );
      }
    },
    startLuck() {
      this.timer = setInterval(
        function () {
          let randomIndex = Math.floor(Math.random() * this.vaildUser.length);
          this.luckMan = this.vaildUser[randomIndex];
        }.bind(this),
        30
      );
    },
    stopLuck() {
      clearInterval(this.timer);
      this.nowRow.prizeUser = this.luckMan;
      this.nowRow = null;
      //从候选者中删除中将人
      const index = this.vaildUser.indexOf(this.luckMan);
      if (index !== -1) {
        this.vaildUser.splice(index, 1);
      }
    },
    //上传文件时处理方法
    handleChange(file, fileList) {
      let fileTemp = file.raw;
      if (fileTemp) {
        const reader = new FileReader();
        const that = this;
        reader.readAsArrayBuffer(fileTemp);
        reader.onload = function () {
          const buffer = reader.result;
          const bytes = new Uint8Array(buffer);
          const length = bytes.byteLength;
          let binary = "";
          for (let i = 0; i < length; i++) {
            binary += String.fromCharCode(bytes[i]);
          }
          const XLSX = require("xlsx");
          const wb = XLSX.read(binary, {
            type: "binary",
          });
          const outdata = XLSX.utils.sheet_to_json(wb.Sheets[wb.SheetNames[0]]);
          that.allUser = [];
          that.prize = [];
          outdata.forEach((item) => {
            if (item["name"]) that.allUser.push(item["name"]);
            if (item["prize"]) {
              that.prize.push({ prizeName: item["prize"], prizeUser: null });
            }
          });
          that.vaildUser = JSON.parse(JSON.stringify(that.allUser));
          that.$message({
            type: "warning",
            message: "更新数据成功",
          });
        };
      } else {
        this.$message({
          type: "warning",
          message: "请上传附件！",
        });
      }
    },
  },
};
</script>

<style>
.el-table__body tr.current-row > td {
  background: #b6aae5 !important;
}
.glass-button {
  display: inline-block;
  background-color: #f0f0f0;
  border: 2px solid #d0d0d0;
  border-radius: 5px;
  box-shadow: 0 2px 5px rgba(0, 0, 0, 0.1);
  color: #333;
  font-size: 16px;
  font-weight: bold;
  line-height: 1;
  padding: 10px 20px;
  text-align: center;
  text-decoration: none;
  transition: all 0.2s ease-in-out;
}

.glass-button:hover {
  background-color: #e0e0e0;
  box-shadow: 0 5px 10px rgba(0, 0, 0, 0.2);
}
/*最外层透明*/
.el-table,
.el-table__expanded-cell {
  background-color: transparent !important;
}
/* 表格内背景颜色 */
.el-table th,
.el-table tr,
.el-table td {
  background-color: transparent !important;
}

.textS {
  font-size: 80px;
  color: #ffd700;
  text-shadow: 5px 4px 4px #c24d00;
  font-weight: bold;
}
.left-span {
  opacity: 0;
  position: fixed;
}
.grid-content {
  border-radius: 4px;
  min-height: 36px;
}
.row-bg {
  padding: 10px 0;
  background-color: rgba(#f9fafc, 0.6);
}
.el-header,
.el-footer {
  background-color: rgba(#b3c0d1, 0.6);
  color: #333;
  text-align: center;
  line-height: 60px;
}
.el-main {
  background-color: rgba(#e9eef3, 0.6);
  color: #333;
  text-align: center;
  line-height: 160px;
}

* {
  margin: 0;
  padding: 0;
  list-style: none;
}
body {
  background: #000;
  height: 100%;
}
#box {
  width: 133px;
  height: 200px;
  position: absolute;
  left: 50%;
  top: 55%;
  margin-left: -75px;
  margin-top: -200px;
  transform: perspective(1000px) rotateX(-10deg);
  transform-style: preserve-3d;
}
#box div {
  position: absolute;
  left: 0;
  top: 0;
  width: 100%;
  height: 100%;
  background: #2e9afe;
  transition: 1s all ease;
  border-radius: 5px;
  box-shadow: 0 0 10px #ffd700;
}
#box div p {
  color: #FFD700fff;
  font-size: 20px;
  position: absolute;
  top: 40%;
  left: 30%;
}

#box div p:first-child {
  color: #FFD700fff;
  font-size: 20px;
  position: absolute;
  top: 5%;
  left: 15%;
}

.prize_k {
  background: url("./assets/zjmd_bg.png");
  background-size: 100% 100%;
}

#app {
  background: url("./assets/background.jpg");
  width: 100%;
  height: 100%;
  position: fixed;
  background-size: 100% 100%;
}

#box div p:last-child {
  color: #FFD700fff;
  font-size: 20px;
  position: absolute;
  top: 60%;
  left: 20%;
}

html {
  height: 100%;
  background: radial-gradient(ellipse at bottom, #1b2735 0%, #090a0f 100%);
  overflow: hidden;
}
#stars {
  width: 1px;
  height: 1px;
  background: transparent;
  box-shadow: 1245px 31px #ffd700, 845px 339px #ffd700, 770px 699px #ffd700,
    1661px 352px #ffd700, 339px 1355px #ffd700, 163px 1713px #ffd700,
    1681px 920px #ffd700, 899px 1695px #ffd700, 1165px 1723px #ffd700,
    1109px 196px #ffd700, 489px 1228px #ffd700, 363px 198px #ffd700,
    762px 611px #ffd700, 264px 1388px #ffd700, 1401px 553px #ffd700,
    1630px 1592px #ffd700, 881px 824px #ffd700, 321px 1683px #ffd700,
    314px 1015px #ffd700, 1297px 1477px #ffd700, 1975px 1013px #ffd700,
    766px 882px #ffd700, 863px 120px #ffd700, 98px 944px #ffd700,
    1192px 891px #ffd700, 1703px 1388px #ffd700, 1173px 172px #ffd700,
    39px 526px #ffd700, 1723px 170px #ffd700, 1228px 1022px #ffd700,
    378px 165px #ffd700, 1594px 476px #ffd700, 603px 621px #ffd700,
    1674px 1922px #ffd700, 1589px 212px #ffd700, 771px 1232px #ffd700,
    1723px 444px #ffd700, 793px 1238px #ffd700, 1111px 1464px #ffd700,
    1071px 1073px #ffd700, 397px 626px #ffd700, 818px 184px #ffd700,
    686px 299px #ffd700, 1179px 1494px #ffd700, 600px 954px #ffd700,
    105px 426px #ffd700, 562px 185px #ffd700, 794px 113px #ffd700,
    1436px 1780px #ffd700, 1980px 1476px #ffd700, 960px 1314px #ffd700,
    1125px 1742px #ffd700, 887px 431px #ffd700, 1078px 1516px #ffd700,
    1547px 1383px #ffd700, 1354px 596px #ffd700, 1052px 1775px #ffd700,
    1996px 227px #ffd700, 1337px 821px #ffd700, 173px 4px #ffd700,
    239px 667px #ffd700, 468px 1813px #ffd700, 9px 303px #ffd700,
    1935px 621px #ffd700, 1602px 1959px #ffd700, 290px 912px #ffd700,
    906px 1542px #ffd700, 670px 1860px #ffd700, 1742px 479px #ffd700,
    1694px 1283px #ffd700, 1986px 427px #ffd700, 1021px 896px #ffd700,
    1758px 227px #ffd700, 1493px 279px #ffd700, 839px 734px #ffd700,
    1314px 1278px #ffd700, 746px 387px #ffd700, 854px 204px #ffd700,
    1366px 1654px #ffd700, 899px 1647px #ffd700, 1307px 570px #ffd700,
    748px 1365px #ffd700, 468px 1238px #ffd700, 832px 223px #ffd700,
    82px 93px #ffd700, 1751px 289px #ffd700, 1246px 1125px #ffd700,
    1140px 1346px #ffd700, 365px 1797px #ffd700, 1912px 1989px #ffd700,
    336px 1573px #ffd700, 1018px 29px #ffd700, 303px 1006px #ffd700,
    1167px 1655px #ffd700, 1385px 438px #ffd700, 1535px 1430px #ffd700,
    620px 736px #ffd700, 836px 342px #ffd700, 970px 1574px #ffd700,
    979px 1582px #ffd700, 1454px 389px #ffd700, 614px 463px #ffd700,
    706px 916px #ffd700, 1964px 1340px #ffd700, 1811px 194px #ffd700,
    1166px 1361px #ffd700, 941px 918px #ffd700, 359px 1715px #ffd700,
    316px 1743px #ffd700, 1462px 1124px #ffd700, 493px 712px #ffd700,
    1570px 960px #ffd700, 1710px 1876px #ffd700, 1127px 1959px #ffd700,
    738px 1033px #ffd700, 1794px 381px #ffd700, 836px 749px #ffd700,
    1657px 375px #ffd700, 525px 212px #ffd700, 1303px 842px #ffd700,
    897px 608px #ffd700, 657px 887px #ffd700, 1232px 133px #ffd700,
    107px 290px #ffd700, 1458px 1751px #ffd700, 128px 1332px #ffd700,
    1127px 782px #ffd700, 911px 1554px #ffd700, 1036px 1191px #ffd700,
    1684px 1260px #ffd700, 592px 1588px #ffd700, 441px 955px #ffd700,
    382px 1871px #ffd700, 1195px 1059px #ffd700, 1677px 325px #ffd700,
    884px 104px #ffd700, 648px 836px #ffd700, 1269px 1504px #ffd700,
    144px 919px #ffd700, 1256px 1311px #ffd700, 516px 1098px #ffd700,
    1367px 1149px #ffd700, 98px 1779px #ffd700, 935px 732px #ffd700,
    61px 1011px #ffd700, 1207px 1843px #ffd700, 1931px 1915px #ffd700,
    754px 1658px #ffd700, 1734px 1698px #ffd700, 1172px 1547px #ffd700,
    962px 584px #ffd700, 152px 1264px #ffd700, 1464px 1931px #ffd700,
    473px 894px #ffd700, 174px 224px #ffd700, 979px 301px #ffd700,
    1839px 1807px #ffd700, 385px 1479px #ffd700, 167px 49px #ffd700,
    64px 434px #ffd700, 1190px 1266px #ffd700, 534px 640px #ffd700,
    1888px 1310px #ffd700, 1283px 1375px #ffd700, 1495px 599px #ffd700,
    1243px 1331px #ffd700, 1559px 1230px #ffd700, 275px 1426px #ffd700,
    1106px 1102px #ffd700, 866px 1421px #ffd700, 1461px 1599px #ffd700,
    1327px 1009px #ffd700, 1786px 800px #ffd700, 881px 733px #ffd700,
    1208px 784px #ffd700, 857px 900px #ffd700, 1122px 77px #ffd700,
    1051px 1352px #ffd700, 1617px 1174px #ffd700, 1988px 1523px #ffd700,
    968px 1167px #ffd700, 1447px 902px #ffd700, 1834px 598px #ffd700,
    1274px 669px #ffd700, 906px 951px #ffd700, 1457px 1597px #ffd700,
    1641px 945px #ffd700, 690px 1052px #ffd700, 1551px 965px #ffd700,
    1468px 1282px #ffd700, 802px 745px #ffd700, 1528px 261px #ffd700,
    1965px 1239px #ffd700, 542px 1686px #ffd700, 866px 611px #ffd700,
    1048px 1938px #ffd700, 710px 63px #ffd700, 1954px 758px #ffd700,
    1416px 993px #ffd700, 1590px 1598px #ffd700, 1497px 1951px #ffd700,
    166px 720px #ffd700, 733px 88px #ffd700, 576px 549px #ffd700,
    284px 1295px #ffd700, 1103px 230px #ffd700, 753px 1653px #ffd700,
    470px 960px #ffd700, 580px 1944px #ffd700, 1131px 1873px #ffd700,
    182px 1859px #ffd700, 383px 834px #ffd700, 510px 474px #ffd700,
    1460px 1471px #ffd700, 1180px 882px #ffd700, 1002px 1471px #ffd700,
    1293px 1102px #ffd700, 305px 778px #ffd700, 890px 1327px #ffd700,
    1826px 1609px #ffd700, 1614px 1448px #ffd700, 1675px 1850px #ffd700,
    1134px 603px #ffd700, 1303px 1988px #ffd700, 1464px 265px #ffd700,
    30px 1107px #ffd700, 98px 981px #ffd700, 290px 580px #ffd700,
    1674px 819px #ffd700, 1245px 82px #ffd700, 396px 1881px #ffd700,
    1150px 1991px #ffd700, 1548px 1584px #ffd700, 1518px 1407px #ffd700,
    1161px 1262px #ffd700, 1011px 274px #ffd700, 899px 293px #ffd700,
    1834px 1811px #ffd700, 1515px 1658px #ffd700, 1510px 1115px #ffd700,
    1376px 1239px #ffd700, 951px 1237px #ffd700, 1369px 1588px #ffd700,
    652px 1123px #ffd700, 1481px 536px #ffd700, 167px 358px #ffd700,
    1231px 1309px #ffd700, 1602px 437px #ffd700, 1153px 672px #ffd700,
    534px 1716px #ffd700, 604px 1687px #ffd700, 1396px 646px #ffd700,
    1417px 1923px #ffd700, 139px 488px #ffd700, 1845px 897px #ffd700,
    415px 1082px #ffd700, 1421px 1914px #ffd700, 1496px 1337px #ffd700,
    254px 1341px #ffd700, 1660px 1416px #ffd700, 732px 1726px #ffd700,
    86px 1921px #ffd700, 90px 151px #ffd700, 728px 288px #ffd700,
    1351px 1093px #ffd700, 393px 1182px #ffd700, 1986px 496px #ffd700,
    667px 110px #ffd700, 682px 643px #ffd700, 77px 1938px #ffd700,
    490px 829px #ffd700, 747px 1449px #ffd700, 36px 788px #ffd700,
    1612px 1658px #ffd700, 1392px 1191px #ffd700, 723px 1748px #ffd700,
    911px 141px #ffd700, 28px 1101px #ffd700, 1360px 665px #ffd700,
    1593px 1751px #ffd700, 411px 936px #ffd700, 1140px 123px #ffd700,
    815px 517px #ffd700, 1696px 880px #ffd700, 731px 1541px #ffd700,
    1958px 792px #ffd700, 52px 1045px #ffd700, 1157px 93px #ffd700,
    1230px 1214px #ffd700, 1433px 1465px #ffd700, 1435px 792px #ffd700,
    1552px 662px #ffd700, 709px 659px #ffd700, 124px 885px #ffd700,
    1351px 535px #ffd700, 1222px 688px #ffd700, 1944px 1453px #ffd700,
    1139px 488px #ffd700, 285px 902px #ffd700, 1781px 104px #ffd700,
    442px 1103px #ffd700, 58px 60px #ffd700, 775px 1496px #ffd700,
    844px 321px #ffd700, 534px 418px #ffd700, 608px 1866px #ffd700,
    562px 684px #ffd700, 1194px 366px #ffd700, 1305px 1753px #ffd700,
    334px 1186px #ffd700, 285px 966px #ffd700, 1431px 1140px #ffd700,
    990px 221px #ffd700, 1720px 347px #ffd700, 159px 1150px #ffd700,
    1329px 345px #ffd700, 1288px 1553px #ffd700, 735px 1060px #ffd700,
    1157px 168px #ffd700, 61px 902px #ffd700, 1602px 1986px #ffd700,
    419px 492px #ffd700, 1797px 1613px #ffd700, 435px 1760px #ffd700,
    1769px 1239px #ffd700, 517px 938px #ffd700, 652px 1887px #ffd700,
    1744px 1544px #ffd700, 784px 1384px #ffd700, 1582px 435px #ffd700,
    1190px 1463px #ffd700, 1497px 577px #ffd700, 571px 297px #ffd700,
    342px 249px #ffd700, 1159px 1786px #ffd700, 222px 1809px #ffd700,
    80px 318px #ffd700, 985px 1972px #ffd700, 1362px 612px #ffd700,
    451px 1760px #ffd700, 1067px 1803px #ffd700, 69px 1556px #ffd700,
    1295px 1381px #ffd700, 930px 1640px #ffd700, 265px 424px #ffd700,
    324px 1109px #ffd700, 587px 743px #ffd700, 1841px 548px #ffd700,
    1215px 1248px #ffd700, 1216px 1791px #ffd700, 698px 1259px #ffd700,
    1309px 952px #ffd700, 943px 1624px #ffd700, 1281px 1582px #ffd700,
    1627px 107px #ffd700, 78px 1617px #ffd700, 152px 275px #ffd700,
    790px 1832px #ffd700, 648px 942px #ffd700, 1471px 1623px #ffd700,
    450px 1042px #ffd700, 194px 1199px #ffd700, 1864px 145px #ffd700,
    965px 296px #ffd700, 1662px 1395px #ffd700, 1840px 857px #ffd700,
    633px 1586px #ffd700, 1007px 1648px #ffd700, 785px 1823px #ffd700,
    1333px 489px #ffd700, 1940px 372px #ffd700, 1592px 1532px #ffd700,
    676px 614px #ffd700, 736px 217px #ffd700, 968px 1073px #ffd700,
    819px 1607px #ffd700, 1017px 1679px #ffd700, 1569px 1747px #ffd700,
    1595px 1027px #ffd700, 915px 1481px #ffd700, 1829px 1234px #ffd700,
    357px 846px #ffd700, 726px 830px #ffd700, 1487px 1865px #ffd700,
    1385px 1417px #ffd700, 1081px 1135px #ffd700, 664px 620px #ffd700,
    1809px 599px #ffd700, 660px 1534px #ffd700, 7px 327px #ffd700,
    80px 1476px #ffd700, 93px 238px #ffd700, 1864px 40px #ffd700,
    1766px 226px #ffd700, 47px 535px #ffd700, 1730px 703px #ffd700,
    924px 1387px #ffd700, 191px 1106px #ffd700, 1094px 1859px #ffd700,
    429px 943px #ffd700, 4px 1302px #ffd700, 1638px 399px #ffd700,
    119px 514px #ffd700, 642px 1005px #ffd700, 1131px 1541px #ffd700,
    434px 1283px #ffd700, 822px 1261px #ffd700, 1515px 1316px #ffd700,
    989px 359px #ffd700, 1785px 88px #ffd700, 1252px 1252px #ffd700,
    1009px 257px #ffd700, 930px 984px #ffd700, 1727px 1569px #ffd700,
    924px 994px #ffd700, 1688px 316px #ffd700, 327px 1287px #ffd700,
    11px 1183px #ffd700, 457px 984px #ffd700, 1818px 113px #ffd700,
    1710px 1428px #ffd700, 1825px 301px #ffd700, 1392px 881px #ffd700,
    707px 1040px #ffd700, 1949px 869px #ffd700, 1146px 1040px #ffd700,
    986px 1200px #ffd700, 1417px 1153px #ffd700, 1933px 1476px #ffd700,
    1939px 1976px #ffd700, 1001px 1662px #ffd700, 1477px 1388px #ffd700,
    1425px 1826px #ffd700, 1656px 1191px #ffd700, 1088px 173px #ffd700,
    445px 135px #ffd700, 1633px 1096px #ffd700, 227px 1933px #ffd700,
    1246px 1093px #ffd700, 1729px 58px #ffd700, 988px 1060px #ffd700,
    1758px 1926px #ffd700, 545px 1954px #ffd700, 950px 1305px #ffd700,
    1947px 1088px #ffd700, 1661px 1990px #ffd700, 880px 1761px #ffd700,
    126px 383px #ffd700, 946px 585px #ffd700, 876px 1339px #ffd700,
    176px 1796px #ffd700, 252px 1880px #ffd700, 615px 1620px #ffd700,
    243px 1782px #ffd700, 1569px 1015px #ffd700, 525px 10px #ffd700,
    1154px 1024px #ffd700, 50px 415px #ffd700, 1993px 510px #ffd700,
    515px 601px #ffd700, 1626px 233px #ffd700, 302px 815px #ffd700,
    158px 1041px #ffd700, 924px 1171px #ffd700, 1618px 1849px #ffd700,
    1476px 817px #ffd700, 22px 481px #ffd700, 573px 657px #ffd700,
    773px 1357px #ffd700, 651px 455px #ffd700, 247px 1523px #ffd700,
    1184px 109px #ffd700, 353px 87px #ffd700, 1141px 355px #ffd700,
    539px 1059px #ffd700, 1307px 1838px #ffd700, 1450px 185px #ffd700,
    1173px 1163px #ffd700, 1472px 566px #ffd700, 1415px 1400px #ffd700,
    1345px 1701px #ffd700, 947px 709px #ffd700, 835px 1896px #ffd700,
    1509px 294px #ffd700, 908px 875px #ffd700, 98px 49px #ffd700,
    26px 577px #ffd700, 1615px 1988px #ffd700, 1045px 1259px #ffd700,
    1894px 1608px #ffd700, 349px 763px #ffd700, 1081px 1217px #ffd700,
    744px 428px #ffd700, 660px 515px #ffd700, 1342px 708px #ffd700,
    758px 929px #ffd700, 502px 143px #ffd700, 1855px 1090px #ffd700,
    678px 1710px #ffd700, 5px 1272px #ffd700, 885px 268px #ffd700,
    1055px 1495px #ffd700, 550px 486px #ffd700, 789px 825px #ffd700,
    602px 238px #ffd700, 781px 1388px #ffd700, 1725px 393px #ffd700,
    617px 10px #ffd700, 1369px 52px #ffd700, 1774px 1833px #ffd700,
    1157px 1144px #ffd700, 983px 1425px #ffd700, 812px 386px #ffd700,
    1410px 1308px #ffd700, 640px 1418px #ffd700, 286px 437px #ffd700,
    1361px 876px #ffd700, 189px 1462px #ffd700, 1491px 78px #ffd700,
    1571px 125px #ffd700, 1491px 896px #ffd700, 1651px 1197px #ffd700,
    881px 1685px #ffd700, 1205px 1290px #ffd700, 178px 673px #ffd700,
    956px 663px #ffd700, 593px 526px #ffd700, 860px 528px #ffd700,
    311px 107px #ffd700, 967px 110px #ffd700, 123px 256px #ffd700,
    742px 1370px #ffd700, 865px 1026px #ffd700, 426px 620px #ffd700,
    149px 1177px #ffd700, 1625px 875px #ffd700, 1251px 292px #ffd700,
    168px 1581px #ffd700, 1080px 1776px #ffd700, 149px 1667px #ffd700,
    1585px 1428px #ffd700, 1096px 1507px #ffd700, 1462px 1074px #ffd700,
    1486px 606px #ffd700, 716px 326px #ffd700, 922px 1531px #ffd700,
    772px 1011px #ffd700, 1013px 1128px #ffd700, 1246px 417px #ffd700,
    1698px 991px #ffd700, 527px 1585px #ffd700, 639px 1057px #ffd700,
    1009px 1216px #ffd700, 199px 1030px #ffd700, 602px 387px #ffd700,
    485px 1099px #ffd700, 201px 1658px #ffd700, 804px 1376px #ffd700,
    993px 1879px #ffd700, 1612px 1447px #ffd700, 39px 713px #ffd700,
    1915px 99px #ffd700, 15px 99px #ffd700, 1962px 1497px #ffd700,
    874px 551px #ffd700, 1575px 1468px #ffd700, 965px 1250px #ffd700,
    1153px 1181px #ffd700, 1395px 1929px #ffd700, 461px 1749px #ffd700,
    942px 1225px #ffd700, 1498px 281px #ffd700, 808px 1510px #ffd700,
    825px 1399px #ffd700, 1097px 1371px #ffd700, 1413px 740px #ffd700,
    1293px 1652px #ffd700, 583px 625px #ffd700, 429px 1976px #ffd700,
    1832px 351px #ffd700, 1356px 1087px #ffd700, 924px 1666px #ffd700,
    1687px 1689px #ffd700, 1546px 1912px #ffd700, 1556px 677px #ffd700,
    1979px 1568px #ffd700, 205px 60px #ffd700, 1437px 456px #ffd700,
    1688px 1515px #ffd700, 1078px 362px #ffd700, 1054px 1126px #ffd700,
    196px 267px #ffd700, 686px 1315px #ffd700, 1314px 202px #ffd700,
    1514px 40px #ffd700, 1411px 1553px #ffd700, 11px 1593px #ffd700,
    301px 587px #ffd700, 1847px 1206px #ffd700, 414px 833px #ffd700,
    1131px 100px #ffd700, 1550px 1187px #ffd700, 1961px 1893px #ffd700,
    1460px 624px #ffd700, 1396px 624px #ffd700, 1005px 1065px #ffd700,
    1129px 1202px #ffd700, 1887px 676px #ffd700, 1243px 1493px #ffd700,
    1877px 1922px #ffd700, 849px 296px #ffd700, 712px 311px #ffd700,
    698px 1983px #ffd700, 1642px 59px #ffd700, 268px 1473px #ffd700,
    1117px 1577px #ffd700, 561px 1884px #ffd700, 1350px 173px #ffd700,
    1407px 1289px #ffd700, 1194px 315px #ffd700, 415px 266px #ffd700,
    1074px 1210px #ffd700, 542px 1955px #ffd700, 1692px 1668px #ffd700,
    507px 171px #ffd700, 644px 78px #ffd700, 1967px 34px #ffd700,
    952px 468px #ffd700, 1259px 1886px #ffd700, 153px 767px #ffd700,
    783px 1340px #ffd700, 1812px 251px #ffd700, 1254px 881px #ffd700,
    1123px 256px #ffd700, 1351px 342px #ffd700, 1853px 1746px #ffd700,
    1672px 12px #ffd700, 1850px 1701px #ffd700, 1674px 478px #ffd700,
    1130px 1924px #ffd700, 1440px 717px #ffd700, 108px 24px #ffd700,
    1393px 89px #ffd700, 197px 55px #ffd700, 1168px 120px #ffd700,
    559px 419px #ffd700, 584px 9px #ffd700, 45px 1728px #ffd700,
    1355px 1731px #ffd700, 644px 1912px #ffd700, 1709px 1652px #ffd700,
    1701px 152px #ffd700, 329px 402px #ffd700, 1467px 185px #ffd700,
    406px 233px #ffd700, 1427px 506px #ffd700, 506px 1537px #ffd700,
    1728px 689px #ffd700, 504px 461px #ffd700, 1949px 1019px #ffd700,
    628px 1504px #ffd700, 1892px 257px #ffd700, 910px 1445px #ffd700,
    1051px 941px #ffd700, 424px 1048px #ffd700, 778px 1433px #ffd700,
    109px 1476px #ffd700, 401px 1185px #ffd700, 130px 767px #ffd700,
    1568px 267px #ffd700, 1497px 1506px #ffd700, 1626px 730px #ffd700,
    229px 1756px #ffd700, 1662px 504px #ffd700, 713px 265px #ffd700,
    1784px 1729px #ffd700, 725px 1425px #ffd700, 1331px 1502px #ffd700,
    96px 8px #ffd700, 1712px 667px #ffd700, 266px 1960px #ffd700,
    391px 651px #ffd700, 729px 1425px #ffd700, 1514px 174px #ffd700,
    1252px 504px #ffd700, 524px 1693px #ffd700, 1509px 856px #ffd700,
    1791px 613px #ffd700, 1656px 361px #ffd700, 752px 1770px #ffd700,
    1237px 937px #ffd700, 350px 482px #ffd700, 793px 1428px #ffd700,
    306px 389px #ffd700, 814px 1851px #ffd700, 207px 66px #ffd700,
    739px 539px #ffd700, 1475px 901px #ffd700, 994px 1498px #ffd700,
    49px 330px #ffd700;
  animation: animStar 50s linear infinite;
}
#stars:after {
  content: " ";
  position: absolute;
  width: 1px;
  height: 1px;
  background: transparent;
  box-shadow: 1245px 31px #ffd700, 845px 339px #ffd700, 770px 699px #ffd700,
    1661px 352px #ffd700, 339px 1355px #ffd700, 163px 1713px #ffd700,
    1681px 920px #ffd700, 899px 1695px #ffd700, 1165px 1723px #ffd700,
    1109px 196px #ffd700, 489px 1228px #ffd700, 363px 198px #ffd700,
    762px 611px #ffd700, 264px 1388px #ffd700, 1401px 553px #ffd700,
    1630px 1592px #ffd700, 881px 824px #ffd700, 321px 1683px #ffd700,
    314px 1015px #ffd700, 1297px 1477px #ffd700, 1975px 1013px #ffd700,
    766px 882px #ffd700, 863px 120px #ffd700, 98px 944px #ffd700,
    1192px 891px #ffd700, 1703px 1388px #ffd700, 1173px 172px #ffd700,
    39px 526px #ffd700, 1723px 170px #ffd700, 1228px 1022px #ffd700,
    378px 165px #ffd700, 1594px 476px #ffd700, 603px 621px #ffd700,
    1674px 1922px #ffd700, 1589px 212px #ffd700, 771px 1232px #ffd700,
    1723px 444px #ffd700, 793px 1238px #ffd700, 1111px 1464px #ffd700,
    1071px 1073px #ffd700, 397px 626px #ffd700, 818px 184px #ffd700,
    686px 299px #ffd700, 1179px 1494px #ffd700, 600px 954px #ffd700,
    105px 426px #ffd700, 562px 185px #ffd700, 794px 113px #ffd700,
    1436px 1780px #ffd700, 1980px 1476px #ffd700, 960px 1314px #ffd700,
    1125px 1742px #ffd700, 887px 431px #ffd700, 1078px 1516px #ffd700,
    1547px 1383px #ffd700, 1354px 596px #ffd700, 1052px 1775px #ffd700,
    1996px 227px #ffd700, 1337px 821px #ffd700, 173px 4px #ffd700,
    239px 667px #ffd700, 468px 1813px #ffd700, 9px 303px #ffd700,
    1935px 621px #ffd700, 1602px 1959px #ffd700, 290px 912px #ffd700,
    906px 1542px #ffd700, 670px 1860px #ffd700, 1742px 479px #ffd700,
    1694px 1283px #ffd700, 1986px 427px #ffd700, 1021px 896px #ffd700,
    1758px 227px #ffd700, 1493px 279px #ffd700, 839px 734px #ffd700,
    1314px 1278px #ffd700, 746px 387px #ffd700, 854px 204px #ffd700,
    1366px 1654px #ffd700, 899px 1647px #ffd700, 1307px 570px #ffd700,
    748px 1365px #ffd700, 468px 1238px #ffd700, 832px 223px #ffd700,
    82px 93px #ffd700, 1751px 289px #ffd700, 1246px 1125px #ffd700,
    1140px 1346px #ffd700, 365px 1797px #ffd700, 1912px 1989px #ffd700,
    336px 1573px #ffd700, 1018px 29px #ffd700, 303px 1006px #ffd700,
    1167px 1655px #ffd700, 1385px 438px #ffd700, 1535px 1430px #ffd700,
    620px 736px #ffd700, 836px 342px #ffd700, 970px 1574px #ffd700,
    979px 1582px #ffd700, 1454px 389px #ffd700, 614px 463px #ffd700,
    706px 916px #ffd700, 1964px 1340px #ffd700, 1811px 194px #ffd700,
    1166px 1361px #ffd700, 941px 918px #ffd700, 359px 1715px #ffd700,
    316px 1743px #ffd700, 1462px 1124px #ffd700, 493px 712px #ffd700,
    1570px 960px #ffd700, 1710px 1876px #ffd700, 1127px 1959px #ffd700,
    738px 1033px #ffd700, 1794px 381px #ffd700, 836px 749px #ffd700,
    1657px 375px #ffd700, 525px 212px #ffd700, 1303px 842px #ffd700,
    897px 608px #ffd700, 657px 887px #ffd700, 1232px 133px #ffd700,
    107px 290px #ffd700, 1458px 1751px #ffd700, 128px 1332px #ffd700,
    1127px 782px #ffd700, 911px 1554px #ffd700, 1036px 1191px #ffd700,
    1684px 1260px #ffd700, 592px 1588px #ffd700, 441px 955px #ffd700,
    382px 1871px #ffd700, 1195px 1059px #ffd700, 1677px 325px #ffd700,
    884px 104px #ffd700, 648px 836px #ffd700, 1269px 1504px #ffd700,
    144px 919px #ffd700, 1256px 1311px #ffd700, 516px 1098px #ffd700,
    1367px 1149px #ffd700, 98px 1779px #ffd700, 935px 732px #ffd700,
    61px 1011px #ffd700, 1207px 1843px #ffd700, 1931px 1915px #ffd700,
    754px 1658px #ffd700, 1734px 1698px #ffd700, 1172px 1547px #ffd700,
    962px 584px #ffd700, 152px 1264px #ffd700, 1464px 1931px #ffd700,
    473px 894px #ffd700, 174px 224px #ffd700, 979px 301px #ffd700,
    1839px 1807px #ffd700, 385px 1479px #ffd700, 167px 49px #ffd700,
    64px 434px #ffd700, 1190px 1266px #ffd700, 534px 640px #ffd700,
    1888px 1310px #ffd700, 1283px 1375px #ffd700, 1495px 599px #ffd700,
    1243px 1331px #ffd700, 1559px 1230px #ffd700, 275px 1426px #ffd700,
    1106px 1102px #ffd700, 866px 1421px #ffd700, 1461px 1599px #ffd700,
    1327px 1009px #ffd700, 1786px 800px #ffd700, 881px 733px #ffd700,
    1208px 784px #ffd700, 857px 900px #ffd700, 1122px 77px #ffd700,
    1051px 1352px #ffd700, 1617px 1174px #ffd700, 1988px 1523px #ffd700,
    968px 1167px #ffd700, 1447px 902px #ffd700, 1834px 598px #ffd700,
    1274px 669px #ffd700, 906px 951px #ffd700, 1457px 1597px #ffd700,
    1641px 945px #ffd700, 690px 1052px #ffd700, 1551px 965px #ffd700,
    1468px 1282px #ffd700, 802px 745px #ffd700, 1528px 261px #ffd700,
    1965px 1239px #ffd700, 542px 1686px #ffd700, 866px 611px #ffd700,
    1048px 1938px #ffd700, 710px 63px #ffd700, 1954px 758px #ffd700,
    1416px 993px #ffd700, 1590px 1598px #ffd700, 1497px 1951px #ffd700,
    166px 720px #ffd700, 733px 88px #ffd700, 576px 549px #ffd700,
    284px 1295px #ffd700, 1103px 230px #ffd700, 753px 1653px #ffd700,
    470px 960px #ffd700, 580px 1944px #ffd700, 1131px 1873px #ffd700,
    182px 1859px #ffd700, 383px 834px #ffd700, 510px 474px #ffd700,
    1460px 1471px #ffd700, 1180px 882px #ffd700, 1002px 1471px #ffd700,
    1293px 1102px #ffd700, 305px 778px #ffd700, 890px 1327px #ffd700,
    1826px 1609px #ffd700, 1614px 1448px #ffd700, 1675px 1850px #ffd700,
    1134px 603px #ffd700, 1303px 1988px #ffd700, 1464px 265px #ffd700,
    30px 1107px #ffd700, 98px 981px #ffd700, 290px 580px #ffd700,
    1674px 819px #ffd700, 1245px 82px #ffd700, 396px 1881px #ffd700,
    1150px 1991px #ffd700, 1548px 1584px #ffd700, 1518px 1407px #ffd700,
    1161px 1262px #ffd700, 1011px 274px #ffd700, 899px 293px #ffd700,
    1834px 1811px #ffd700, 1515px 1658px #ffd700, 1510px 1115px #ffd700,
    1376px 1239px #ffd700, 951px 1237px #ffd700, 1369px 1588px #ffd700,
    652px 1123px #ffd700, 1481px 536px #ffd700, 167px 358px #ffd700,
    1231px 1309px #ffd700, 1602px 437px #ffd700, 1153px 672px #ffd700,
    534px 1716px #ffd700, 604px 1687px #ffd700, 1396px 646px #ffd700,
    1417px 1923px #ffd700, 139px 488px #ffd700, 1845px 897px #ffd700,
    415px 1082px #ffd700, 1421px 1914px #ffd700, 1496px 1337px #ffd700,
    254px 1341px #ffd700, 1660px 1416px #ffd700, 732px 1726px #ffd700,
    86px 1921px #ffd700, 90px 151px #ffd700, 728px 288px #ffd700,
    1351px 1093px #ffd700, 393px 1182px #ffd700, 1986px 496px #ffd700,
    667px 110px #ffd700, 682px 643px #ffd700, 77px 1938px #ffd700,
    490px 829px #ffd700, 747px 1449px #ffd700, 36px 788px #ffd700,
    1612px 1658px #ffd700, 1392px 1191px #ffd700, 723px 1748px #ffd700,
    911px 141px #ffd700, 28px 1101px #ffd700, 1360px 665px #ffd700,
    1593px 1751px #ffd700, 411px 936px #ffd700, 1140px 123px #ffd700,
    815px 517px #ffd700, 1696px 880px #ffd700, 731px 1541px #ffd700,
    1958px 792px #ffd700, 52px 1045px #ffd700, 1157px 93px #ffd700,
    1230px 1214px #ffd700, 1433px 1465px #ffd700, 1435px 792px #ffd700,
    1552px 662px #ffd700, 709px 659px #ffd700, 124px 885px #ffd700,
    1351px 535px #ffd700, 1222px 688px #ffd700, 1944px 1453px #ffd700,
    1139px 488px #ffd700, 285px 902px #ffd700, 1781px 104px #ffd700,
    442px 1103px #ffd700, 58px 60px #ffd700, 775px 1496px #ffd700,
    844px 321px #ffd700, 534px 418px #ffd700, 608px 1866px #ffd700,
    562px 684px #ffd700, 1194px 366px #ffd700, 1305px 1753px #ffd700,
    334px 1186px #ffd700, 285px 966px #ffd700, 1431px 1140px #ffd700,
    990px 221px #ffd700, 1720px 347px #ffd700, 159px 1150px #ffd700,
    1329px 345px #ffd700, 1288px 1553px #ffd700, 735px 1060px #ffd700,
    1157px 168px #ffd700, 61px 902px #ffd700, 1602px 1986px #ffd700,
    419px 492px #ffd700, 1797px 1613px #ffd700, 435px 1760px #ffd700,
    1769px 1239px #ffd700, 517px 938px #ffd700, 652px 1887px #ffd700,
    1744px 1544px #ffd700, 784px 1384px #ffd700, 1582px 435px #ffd700,
    1190px 1463px #ffd700, 1497px 577px #ffd700, 571px 297px #ffd700,
    342px 249px #ffd700, 1159px 1786px #ffd700, 222px 1809px #ffd700,
    80px 318px #ffd700, 985px 1972px #ffd700, 1362px 612px #ffd700,
    451px 1760px #ffd700, 1067px 1803px #ffd700, 69px 1556px #ffd700,
    1295px 1381px #ffd700, 930px 1640px #ffd700, 265px 424px #ffd700,
    324px 1109px #ffd700, 587px 743px #ffd700, 1841px 548px #ffd700,
    1215px 1248px #ffd700, 1216px 1791px #ffd700, 698px 1259px #ffd700,
    1309px 952px #ffd700, 943px 1624px #ffd700, 1281px 1582px #ffd700,
    1627px 107px #ffd700, 78px 1617px #ffd700, 152px 275px #ffd700,
    790px 1832px #ffd700, 648px 942px #ffd700, 1471px 1623px #ffd700,
    450px 1042px #ffd700, 194px 1199px #ffd700, 1864px 145px #ffd700,
    965px 296px #ffd700, 1662px 1395px #ffd700, 1840px 857px #ffd700,
    633px 1586px #ffd700, 1007px 1648px #ffd700, 785px 1823px #ffd700,
    1333px 489px #ffd700, 1940px 372px #ffd700, 1592px 1532px #ffd700,
    676px 614px #ffd700, 736px 217px #ffd700, 968px 1073px #ffd700,
    819px 1607px #ffd700, 1017px 1679px #ffd700, 1569px 1747px #ffd700,
    1595px 1027px #ffd700, 915px 1481px #ffd700, 1829px 1234px #ffd700,
    357px 846px #ffd700, 726px 830px #ffd700, 1487px 1865px #ffd700,
    1385px 1417px #ffd700, 1081px 1135px #ffd700, 664px 620px #ffd700,
    1809px 599px #ffd700, 660px 1534px #ffd700, 7px 327px #ffd700,
    80px 1476px #ffd700, 93px 238px #ffd700, 1864px 40px #ffd700,
    1766px 226px #ffd700, 47px 535px #ffd700, 1730px 703px #ffd700,
    924px 1387px #ffd700, 191px 1106px #ffd700, 1094px 1859px #ffd700,
    429px 943px #ffd700, 4px 1302px #ffd700, 1638px 399px #ffd700,
    119px 514px #ffd700, 642px 1005px #ffd700, 1131px 1541px #ffd700,
    434px 1283px #ffd700, 822px 1261px #ffd700, 1515px 1316px #ffd700,
    989px 359px #ffd700, 1785px 88px #ffd700, 1252px 1252px #ffd700,
    1009px 257px #ffd700, 930px 984px #ffd700, 1727px 1569px #ffd700,
    924px 994px #ffd700, 1688px 316px #ffd700, 327px 1287px #ffd700,
    11px 1183px #ffd700, 457px 984px #ffd700, 1818px 113px #ffd700,
    1710px 1428px #ffd700, 1825px 301px #ffd700, 1392px 881px #ffd700,
    707px 1040px #ffd700, 1949px 869px #ffd700, 1146px 1040px #ffd700,
    986px 1200px #ffd700, 1417px 1153px #ffd700, 1933px 1476px #ffd700,
    1939px 1976px #ffd700, 1001px 1662px #ffd700, 1477px 1388px #ffd700,
    1425px 1826px #ffd700, 1656px 1191px #ffd700, 1088px 173px #ffd700,
    445px 135px #ffd700, 1633px 1096px #ffd700, 227px 1933px #ffd700,
    1246px 1093px #ffd700, 1729px 58px #ffd700, 988px 1060px #ffd700,
    1758px 1926px #ffd700, 545px 1954px #ffd700, 950px 1305px #ffd700,
    1947px 1088px #ffd700, 1661px 1990px #ffd700, 880px 1761px #ffd700,
    126px 383px #ffd700, 946px 585px #ffd700, 876px 1339px #ffd700,
    176px 1796px #ffd700, 252px 1880px #ffd700, 615px 1620px #ffd700,
    243px 1782px #ffd700, 1569px 1015px #ffd700, 525px 10px #ffd700,
    1154px 1024px #ffd700, 50px 415px #ffd700, 1993px 510px #ffd700,
    515px 601px #ffd700, 1626px 233px #ffd700, 302px 815px #ffd700,
    158px 1041px #ffd700, 924px 1171px #ffd700, 1618px 1849px #ffd700,
    1476px 817px #ffd700, 22px 481px #ffd700, 573px 657px #ffd700,
    773px 1357px #ffd700, 651px 455px #ffd700, 247px 1523px #ffd700,
    1184px 109px #ffd700, 353px 87px #ffd700, 1141px 355px #ffd700,
    539px 1059px #ffd700, 1307px 1838px #ffd700, 1450px 185px #ffd700,
    1173px 1163px #ffd700, 1472px 566px #ffd700, 1415px 1400px #ffd700,
    1345px 1701px #ffd700, 947px 709px #ffd700, 835px 1896px #ffd700,
    1509px 294px #ffd700, 908px 875px #ffd700, 98px 49px #ffd700,
    26px 577px #ffd700, 1615px 1988px #ffd700, 1045px 1259px #ffd700,
    1894px 1608px #ffd700, 349px 763px #ffd700, 1081px 1217px #ffd700,
    744px 428px #ffd700, 660px 515px #ffd700, 1342px 708px #ffd700,
    758px 929px #ffd700, 502px 143px #ffd700, 1855px 1090px #ffd700,
    678px 1710px #ffd700, 5px 1272px #ffd700, 885px 268px #ffd700,
    1055px 1495px #ffd700, 550px 486px #ffd700, 789px 825px #ffd700,
    602px 238px #ffd700, 781px 1388px #ffd700, 1725px 393px #ffd700,
    617px 10px #ffd700, 1369px 52px #ffd700, 1774px 1833px #ffd700,
    1157px 1144px #ffd700, 983px 1425px #ffd700, 812px 386px #ffd700,
    1410px 1308px #ffd700, 640px 1418px #ffd700, 286px 437px #ffd700,
    1361px 876px #ffd700, 189px 1462px #ffd700, 1491px 78px #ffd700,
    1571px 125px #ffd700, 1491px 896px #ffd700, 1651px 1197px #ffd700,
    881px 1685px #ffd700, 1205px 1290px #ffd700, 178px 673px #ffd700,
    956px 663px #ffd700, 593px 526px #ffd700, 860px 528px #ffd700,
    311px 107px #ffd700, 967px 110px #ffd700, 123px 256px #ffd700,
    742px 1370px #ffd700, 865px 1026px #ffd700, 426px 620px #ffd700,
    149px 1177px #ffd700, 1625px 875px #ffd700, 1251px 292px #ffd700,
    168px 1581px #ffd700, 1080px 1776px #ffd700, 149px 1667px #ffd700,
    1585px 1428px #ffd700, 1096px 1507px #ffd700, 1462px 1074px #ffd700,
    1486px 606px #ffd700, 716px 326px #ffd700, 922px 1531px #ffd700,
    772px 1011px #ffd700, 1013px 1128px #ffd700, 1246px 417px #ffd700,
    1698px 991px #ffd700, 527px 1585px #ffd700, 639px 1057px #ffd700,
    1009px 1216px #ffd700, 199px 1030px #ffd700, 602px 387px #ffd700,
    485px 1099px #ffd700, 201px 1658px #ffd700, 804px 1376px #ffd700,
    993px 1879px #ffd700, 1612px 1447px #ffd700, 39px 713px #ffd700,
    1915px 99px #ffd700, 15px 99px #ffd700, 1962px 1497px #ffd700,
    874px 551px #ffd700, 1575px 1468px #ffd700, 965px 1250px #ffd700,
    1153px 1181px #ffd700, 1395px 1929px #ffd700, 461px 1749px #ffd700,
    942px 1225px #ffd700, 1498px 281px #ffd700, 808px 1510px #ffd700,
    825px 1399px #ffd700, 1097px 1371px #ffd700, 1413px 740px #ffd700,
    1293px 1652px #ffd700, 583px 625px #ffd700, 429px 1976px #ffd700,
    1832px 351px #ffd700, 1356px 1087px #ffd700, 924px 1666px #ffd700,
    1687px 1689px #ffd700, 1546px 1912px #ffd700, 1556px 677px #ffd700,
    1979px 1568px #ffd700, 205px 60px #ffd700, 1437px 456px #ffd700,
    1688px 1515px #ffd700, 1078px 362px #ffd700, 1054px 1126px #ffd700,
    196px 267px #ffd700, 686px 1315px #ffd700, 1314px 202px #ffd700,
    1514px 40px #ffd700, 1411px 1553px #ffd700, 11px 1593px #ffd700,
    301px 587px #ffd700, 1847px 1206px #ffd700, 414px 833px #ffd700,
    1131px 100px #ffd700, 1550px 1187px #ffd700, 1961px 1893px #ffd700,
    1460px 624px #ffd700, 1396px 624px #ffd700, 1005px 1065px #ffd700,
    1129px 1202px #ffd700, 1887px 676px #ffd700, 1243px 1493px #ffd700,
    1877px 1922px #ffd700, 849px 296px #ffd700, 712px 311px #ffd700,
    698px 1983px #ffd700, 1642px 59px #ffd700, 268px 1473px #ffd700,
    1117px 1577px #ffd700, 561px 1884px #ffd700, 1350px 173px #ffd700,
    1407px 1289px #ffd700, 1194px 315px #ffd700, 415px 266px #ffd700,
    1074px 1210px #ffd700, 542px 1955px #ffd700, 1692px 1668px #ffd700,
    507px 171px #ffd700, 644px 78px #ffd700, 1967px 34px #ffd700,
    952px 468px #ffd700, 1259px 1886px #ffd700, 153px 767px #ffd700,
    783px 1340px #ffd700, 1812px 251px #ffd700, 1254px 881px #ffd700,
    1123px 256px #ffd700, 1351px 342px #ffd700, 1853px 1746px #ffd700,
    1672px 12px #ffd700, 1850px 1701px #ffd700, 1674px 478px #ffd700,
    1130px 1924px #ffd700, 1440px 717px #ffd700, 108px 24px #ffd700,
    1393px 89px #ffd700, 197px 55px #ffd700, 1168px 120px #ffd700,
    559px 419px #ffd700, 584px 9px #ffd700, 45px 1728px #ffd700,
    1355px 1731px #ffd700, 644px 1912px #ffd700, 1709px 1652px #ffd700,
    1701px 152px #ffd700, 329px 402px #ffd700, 1467px 185px #ffd700,
    406px 233px #ffd700, 1427px 506px #ffd700, 506px 1537px #ffd700,
    1728px 689px #ffd700, 504px 461px #ffd700, 1949px 1019px #ffd700,
    628px 1504px #ffd700, 1892px 257px #ffd700, 910px 1445px #ffd700,
    1051px 941px #ffd700, 424px 1048px #ffd700, 778px 1433px #ffd700,
    109px 1476px #ffd700, 401px 1185px #ffd700, 130px 767px #ffd700,
    1568px 267px #ffd700, 1497px 1506px #ffd700, 1626px 730px #ffd700,
    229px 1756px #ffd700, 1662px 504px #ffd700, 713px 265px #ffd700,
    1784px 1729px #ffd700, 725px 1425px #ffd700, 1331px 1502px #ffd700,
    96px 8px #ffd700, 1712px 667px #ffd700, 266px 1960px #ffd700,
    391px 651px #ffd700, 729px 1425px #ffd700, 1514px 174px #ffd700,
    1252px 504px #ffd700, 524px 1693px #ffd700, 1509px 856px #ffd700,
    1791px 613px #ffd700, 1656px 361px #ffd700, 752px 1770px #ffd700,
    1237px 937px #ffd700, 350px 482px #ffd700, 793px 1428px #ffd700,
    306px 389px #ffd700, 814px 1851px #ffd700, 207px 66px #ffd700,
    739px 539px #ffd700, 1475px 901px #ffd700, 994px 1498px #ffd700,
    49px 330px #ffd700;
}
#stars2 {
  width: 2px;
  height: 2px;
  background: transparent;
  box-shadow: 1221px 1713px #ffd700, 1284px 710px #ffd700, 1135px 1773px #ffd700,
    923px 55px #ffd700, 988px 1462px #ffd700, 567px 622px #ffd700,
    1634px 1569px #ffd700, 812px 588px #ffd700, 492px 1240px #ffd700,
    1092px 1902px #ffd700, 536px 933px #ffd700, 256px 66px #ffd700,
    86px 735px #ffd700, 1484px 38px #ffd700, 460px 720px #ffd700,
    544px 1890px #ffd700, 1524px 746px #ffd700, 1111px 1361px #ffd700,
    210px 609px #ffd700, 1782px 1349px #ffd700, 1698px 1848px #ffd700,
    35px 1588px #ffd700, 507px 367px #ffd700, 918px 1670px #ffd700,
    1564px 1733px #ffd700, 650px 214px #ffd700, 290px 418px #ffd700,
    232px 853px #ffd700, 1326px 1430px #ffd700, 1767px 106px #ffd700,
    107px 520px #ffd700, 1588px 845px #ffd700, 1989px 1883px #ffd700,
    662px 1538px #ffd700, 173px 968px #ffd700, 210px 1697px #ffd700,
    698px 847px #ffd700, 92px 118px #ffd700, 925px 254px #ffd700,
    649px 1967px #ffd700, 482px 1395px #ffd700, 607px 830px #ffd700,
    1795px 423px #ffd700, 809px 1656px #ffd700, 1606px 1527px #ffd700,
    541px 485px #ffd700, 1243px 282px #ffd700, 1047px 605px #ffd700,
    1771px 1581px #ffd700, 89px 1709px #ffd700, 1106px 776px #ffd700,
    1756px 136px #ffd700, 957px 1829px #ffd700, 1092px 529px #ffd700,
    1142px 493px #ffd700, 657px 643px #ffd700, 1957px 753px #ffd700,
    935px 219px #ffd700, 1197px 1971px #ffd700, 336px 1394px #ffd700,
    1451px 215px #ffd700, 899px 541px #ffd700, 190px 1411px #ffd700,
    1012px 1121px #ffd700, 1914px 361px #ffd700, 231px 71px #ffd700,
    323px 1631px #ffd700, 183px 1377px #ffd700, 98px 765px #ffd700,
    1520px 320px #ffd700, 556px 77px #ffd700, 588px 1518px #ffd700,
    1177px 1487px #ffd700, 1172px 1426px #ffd700, 243px 520px #ffd700,
    25px 296px #ffd700, 1021px 344px #ffd700, 1416px 63px #ffd700,
    1624px 1590px #ffd700, 1198px 224px #ffd700, 72px 136px #ffd700,
    347px 1099px #ffd700, 1240px 113px #ffd700, 506px 1883px #ffd700,
    623px 1889px #ffd700, 507px 139px #ffd700, 531px 96px #ffd700,
    436px 398px #ffd700, 1471px 1957px #ffd700, 769px 1696px #ffd700,
    774px 973px #ffd700, 698px 1904px #ffd700, 1616px 1098px #ffd700,
    1560px 1560px #ffd700, 1282px 1888px #ffd700, 1733px 1275px #ffd700,
    1602px 1509px #ffd700, 1579px 220px #ffd700, 1860px 1609px #ffd700,
    1157px 1685px #ffd700, 1278px 1172px #ffd700, 758px 277px #ffd700,
    1676px 1446px #ffd700, 532px 1445px #ffd700, 1285px 1024px #ffd700,
    1013px 576px #ffd700, 635px 1717px #ffd700, 546px 1058px #ffd700,
    537px 1261px #ffd700, 548px 1134px #ffd700, 919px 1738px #ffd700,
    889px 1641px #ffd700, 944px 1035px #ffd700, 1354px 1298px #ffd700,
    1539px 1950px #ffd700, 1157px 1710px #ffd700, 442px 1488px #ffd700,
    703px 936px #ffd700, 1395px 226px #ffd700, 1946px 783px #ffd700,
    420px 466px #ffd700, 100px 743px #ffd700, 361px 523px #ffd700,
    1218px 151px #ffd700, 395px 799px #ffd700, 1977px 1741px #ffd700,
    1570px 991px #ffd700, 1124px 375px #ffd700, 1414px 1520px #ffd700,
    1535px 884px #ffd700, 1572px 1672px #ffd700, 1828px 325px #ffd700,
    650px 128px #ffd700, 1650px 1509px #ffd700, 11px 1725px #ffd700,
    383px 1595px #ffd700, 1705px 944px #ffd700, 472px 1684px #ffd700,
    1406px 612px #ffd700, 1702px 17px #ffd700, 214px 1801px #ffd700,
    1126px 1647px #ffd700, 1454px 1588px #ffd700, 210px 1734px #ffd700,
    1937px 685px #ffd700, 1563px 1972px #ffd700, 1830px 216px #ffd700,
    965px 1699px #ffd700, 1077px 467px #ffd700, 460px 1225px #ffd700,
    953px 45px #ffd700, 1655px 936px #ffd700, 735px 1358px #ffd700,
    557px 643px #ffd700, 718px 147px #ffd700, 1607px 1301px #ffd700,
    356px 338px #ffd700, 661px 1688px #ffd700, 1222px 1982px #ffd700,
    1892px 1851px #ffd700, 1419px 998px #ffd700, 801px 1986px #ffd700,
    70px 410px #ffd700, 749px 1282px #ffd700, 1952px 850px #ffd700,
    1925px 1px #ffd700, 1478px 1744px #ffd700, 193px 393px #ffd700,
    1501px 1712px #ffd700, 1514px 843px #ffd700, 5px 440px #ffd700,
    999px 1317px #ffd700, 572px 1005px #ffd700, 1035px 1755px #ffd700,
    619px 1703px #ffd700, 664px 1405px #ffd700, 404px 1649px #ffd700,
    1247px 1359px #ffd700, 516px 1296px #ffd700, 644px 23px #ffd700,
    1683px 1026px #ffd700, 623px 897px #ffd700, 1727px 1580px #ffd700,
    1838px 1281px #ffd700, 1065px 85px #ffd700, 1175px 1054px #ffd700,
    480px 147px #ffd700, 1778px 865px #ffd700, 489px 1065px #ffd700,
    767px 896px #ffd700, 100px 981px #ffd700, 788px 286px #ffd700,
    323px 769px #ffd700, 1470px 1743px #ffd700, 289px 910px #ffd700,
    1053px 116px #ffd700, 1455px 1242px #ffd700, 1213px 1249px #ffd700,
    301px 561px #ffd700, 288px 301px #ffd700;
  animation: animStar 100s linear infinite;
}
#stars2:after {
  content: " ";
  position: absolute;
  width: 2px;
  height: 2px;
  background: transparent;
  box-shadow: 1221px 1713px #ffd700, 1284px 710px #ffd700, 1135px 1773px #ffd700,
    923px 55px #ffd700, 988px 1462px #ffd700, 567px 622px #ffd700,
    1634px 1569px #ffd700, 812px 588px #ffd700, 492px 1240px #ffd700,
    1092px 1902px #ffd700, 536px 933px #ffd700, 256px 66px #ffd700,
    86px 735px #ffd700, 1484px 38px #ffd700, 460px 720px #ffd700,
    544px 1890px #ffd700, 1524px 746px #ffd700, 1111px 1361px #ffd700,
    210px 609px #ffd700, 1782px 1349px #ffd700, 1698px 1848px #ffd700,
    35px 1588px #ffd700, 507px 367px #ffd700, 918px 1670px #ffd700,
    1564px 1733px #ffd700, 650px 214px #ffd700, 290px 418px #ffd700,
    232px 853px #ffd700, 1326px 1430px #ffd700, 1767px 106px #ffd700,
    107px 520px #ffd700, 1588px 845px #ffd700, 1989px 1883px #ffd700,
    662px 1538px #ffd700, 173px 968px #ffd700, 210px 1697px #ffd700,
    698px 847px #ffd700, 92px 118px #ffd700, 925px 254px #ffd700,
    649px 1967px #ffd700, 482px 1395px #ffd700, 607px 830px #ffd700,
    1795px 423px #ffd700, 809px 1656px #ffd700, 1606px 1527px #ffd700,
    541px 485px #ffd700, 1243px 282px #ffd700, 1047px 605px #ffd700,
    1771px 1581px #ffd700, 89px 1709px #ffd700, 1106px 776px #ffd700,
    1756px 136px #ffd700, 957px 1829px #ffd700, 1092px 529px #ffd700,
    1142px 493px #ffd700, 657px 643px #ffd700, 1957px 753px #ffd700,
    935px 219px #ffd700, 1197px 1971px #ffd700, 336px 1394px #ffd700,
    1451px 215px #ffd700, 899px 541px #ffd700, 190px 1411px #ffd700,
    1012px 1121px #ffd700, 1914px 361px #ffd700, 231px 71px #ffd700,
    323px 1631px #ffd700, 183px 1377px #ffd700, 98px 765px #ffd700,
    1520px 320px #ffd700, 556px 77px #ffd700, 588px 1518px #ffd700,
    1177px 1487px #ffd700, 1172px 1426px #ffd700, 243px 520px #ffd700,
    25px 296px #ffd700, 1021px 344px #ffd700, 1416px 63px #ffd700,
    1624px 1590px #ffd700, 1198px 224px #ffd700, 72px 136px #ffd700,
    347px 1099px #ffd700, 1240px 113px #ffd700, 506px 1883px #ffd700,
    623px 1889px #ffd700, 507px 139px #ffd700, 531px 96px #ffd700,
    436px 398px #ffd700, 1471px 1957px #ffd700, 769px 1696px #ffd700,
    774px 973px #ffd700, 698px 1904px #ffd700, 1616px 1098px #ffd700,
    1560px 1560px #ffd700, 1282px 1888px #ffd700, 1733px 1275px #ffd700,
    1602px 1509px #ffd700, 1579px 220px #ffd700, 1860px 1609px #ffd700,
    1157px 1685px #ffd700, 1278px 1172px #ffd700, 758px 277px #ffd700,
    1676px 1446px #ffd700, 532px 1445px #ffd700, 1285px 1024px #ffd700,
    1013px 576px #ffd700, 635px 1717px #ffd700, 546px 1058px #ffd700,
    537px 1261px #ffd700, 548px 1134px #ffd700, 919px 1738px #ffd700,
    889px 1641px #ffd700, 944px 1035px #ffd700, 1354px 1298px #ffd700,
    1539px 1950px #ffd700, 1157px 1710px #ffd700, 442px 1488px #ffd700,
    703px 936px #ffd700, 1395px 226px #ffd700, 1946px 783px #ffd700,
    420px 466px #ffd700, 100px 743px #ffd700, 361px 523px #ffd700,
    1218px 151px #ffd700, 395px 799px #ffd700, 1977px 1741px #ffd700,
    1570px 991px #ffd700, 1124px 375px #ffd700, 1414px 1520px #ffd700,
    1535px 884px #ffd700, 1572px 1672px #ffd700, 1828px 325px #ffd700,
    650px 128px #ffd700, 1650px 1509px #ffd700, 11px 1725px #ffd700,
    383px 1595px #ffd700, 1705px 944px #ffd700, 472px 1684px #ffd700,
    1406px 612px #ffd700, 1702px 17px #ffd700, 214px 1801px #ffd700,
    1126px 1647px #ffd700, 1454px 1588px #ffd700, 210px 1734px #ffd700,
    1937px 685px #ffd700, 1563px 1972px #ffd700, 1830px 216px #ffd700,
    965px 1699px #ffd700, 1077px 467px #ffd700, 460px 1225px #ffd700,
    953px 45px #ffd700, 1655px 936px #ffd700, 735px 1358px #ffd700,
    557px 643px #ffd700, 718px 147px #ffd700, 1607px 1301px #ffd700,
    356px 338px #ffd700, 661px 1688px #ffd700, 1222px 1982px #ffd700,
    1892px 1851px #ffd700, 1419px 998px #ffd700, 801px 1986px #ffd700,
    70px 410px #ffd700, 749px 1282px #ffd700, 1952px 850px #ffd700,
    1925px 1px #ffd700, 1478px 1744px #ffd700, 193px 393px #ffd700,
    1501px 1712px #ffd700, 1514px 843px #ffd700, 5px 440px #ffd700,
    999px 1317px #ffd700, 572px 1005px #ffd700, 1035px 1755px #ffd700,
    619px 1703px #ffd700, 664px 1405px #ffd700, 404px 1649px #ffd700,
    1247px 1359px #ffd700, 516px 1296px #ffd700, 644px 23px #ffd700,
    1683px 1026px #ffd700, 623px 897px #ffd700, 1727px 1580px #ffd700,
    1838px 1281px #ffd700, 1065px 85px #ffd700, 1175px 1054px #ffd700,
    480px 147px #ffd700, 1778px 865px #ffd700, 489px 1065px #ffd700,
    767px 896px #ffd700, 100px 981px #ffd700, 788px 286px #ffd700,
    323px 769px #ffd700, 1470px 1743px #ffd700, 289px 910px #ffd700,
    1053px 116px #ffd700, 1455px 1242px #ffd700, 1213px 1249px #ffd700,
    301px 561px #ffd700, 288px 301px #ffd700;
}
#stars3 {
  width: 3px;
  height: 3px;
  background: transparent;
  box-shadow: 765px 271px #ffd700, 1412px 1921px #ffd700, 1809px 339px #ffd700,
    1582px 36px #ffd700, 462px 1589px #ffd700, 1025px 1178px #ffd700,
    826px 1714px #ffd700, 1756px 316px #ffd700, 214px 1719px #ffd700,
    446px 812px #ffd700, 1094px 908px #ffd700, 1944px 2px #ffd700,
    1139px 1913px #ffd700, 499px 2px #ffd700, 112px 1939px #ffd700,
    766px 513px #ffd700, 1324px 70px #ffd700, 1850px 650px #ffd700,
    392px 320px #ffd700, 834px 1855px #ffd700, 165px 221px #ffd700,
    81px 1914px #ffd700, 1911px 237px #ffd700, 1813px 1021px #ffd700,
    15px 1572px #ffd700, 1895px 1844px #ffd700, 44px 538px #ffd700,
    1226px 1436px #ffd700, 667px 1295px #ffd700, 1906px 1610px #ffd700,
    593px 221px #ffd700, 968px 1407px #ffd700, 992px 1538px #ffd700,
    726px 248px #ffd700, 1273px 179px #ffd700, 138px 1186px #ffd700,
    395px 785px #ffd700, 1644px 780px #ffd700, 31px 1348px #ffd700,
    1942px 87px #ffd700, 718px 1015px #ffd700, 1323px 107px #ffd700,
    1436px 421px #ffd700, 75px 1863px #ffd700, 1269px 557px #ffd700,
    1162px 1036px #ffd700, 257px 669px #ffd700, 827px 1098px #ffd700,
    138px 1908px #ffd700, 439px 527px #ffd700, 76px 295px #ffd700,
    1266px 248px #ffd700, 1940px 718px #ffd700, 1214px 265px #ffd700,
    37px 1486px #ffd700, 1937px 290px #ffd700, 1129px 785px #ffd700,
    1261px 1650px #ffd700, 144px 219px #ffd700, 1341px 606px #ffd700,
    1317px 872px #ffd700, 1421px 693px #ffd700, 496px 1803px #ffd700,
    1055px 1732px #ffd700, 1448px 302px #ffd700, 774px 495px #ffd700,
    266px 1037px #ffd700, 941px 792px #ffd700, 779px 1034px #ffd700,
    1333px 1194px #ffd700, 1546px 188px #ffd700, 947px 1906px #ffd700,
    1769px 1595px #ffd700, 1667px 724px #ffd700, 699px 1979px #ffd700,
    194px 213px #ffd700, 1984px 1738px #ffd700, 466px 1754px #ffd700,
    1998px 1908px #ffd700, 975px 1413px #ffd700, 1505px 752px #ffd700,
    1341px 932px #ffd700, 1260px 812px #ffd700, 1591px 50px #ffd700,
    1655px 1730px #ffd700, 1955px 1340px #ffd700, 859px 1742px #ffd700,
    1608px 1931px #ffd700, 1431px 1227px #ffd700, 1553px 222px #ffd700,
    684px 1866px #ffd700, 371px 59px #ffd700, 1885px 869px #ffd700,
    1887px 1795px #ffd700, 1032px 1431px #ffd700, 1085px 1840px #ffd700,
    817px 1343px #ffd700, 1260px 1135px #ffd700, 124px 1566px #ffd700,
    1975px 1980px #ffd700;
  animation: animStar 150s linear infinite;
}
#stars3:after {
  content: " ";
  position: absolute;
  width: 3px;
  height: 3px;
  background: transparent;
  box-shadow: 765px 271px #ffd700, 1412px 1921px #ffd700, 1809px 339px #ffd700,
    1582px 36px #ffd700, 462px 1589px #ffd700, 1025px 1178px #ffd700,
    826px 1714px #ffd700, 1756px 316px #ffd700, 214px 1719px #ffd700,
    446px 812px #ffd700, 1094px 908px #ffd700, 1944px 2px #ffd700,
    1139px 1913px #ffd700, 499px 2px #ffd700, 112px 1939px #ffd700,
    766px 513px #ffd700, 1324px 70px #ffd700, 1850px 650px #ffd700,
    392px 320px #ffd700, 834px 1855px #ffd700, 165px 221px #ffd700,
    81px 1914px #ffd700, 1911px 237px #ffd700, 1813px 1021px #ffd700,
    15px 1572px #ffd700, 1895px 1844px #ffd700, 44px 538px #ffd700,
    1226px 1436px #ffd700, 667px 1295px #ffd700, 1906px 1610px #ffd700,
    593px 221px #ffd700, 968px 1407px #ffd700, 992px 1538px #ffd700,
    726px 248px #ffd700, 1273px 179px #ffd700, 138px 1186px #ffd700,
    395px 785px #ffd700, 1644px 780px #ffd700, 31px 1348px #ffd700,
    1942px 87px #ffd700, 718px 1015px #ffd700, 1323px 107px #ffd700,
    1436px 421px #ffd700, 75px 1863px #ffd700, 1269px 557px #ffd700,
    1162px 1036px #ffd700, 257px 669px #ffd700, 827px 1098px #ffd700,
    138px 1908px #ffd700, 439px 527px #ffd700, 76px 295px #ffd700,
    1266px 248px #ffd700, 1940px 718px #ffd700, 1214px 265px #ffd700,
    37px 1486px #ffd700, 1937px 290px #ffd700, 1129px 785px #ffd700,
    1261px 1650px #ffd700, 144px 219px #ffd700, 1341px 606px #ffd700,
    1317px 872px #ffd700, 1421px 693px #ffd700, 496px 1803px #ffd700,
    1055px 1732px #ffd700, 1448px 302px #ffd700, 774px 495px #ffd700,
    266px 1037px #ffd700, 941px 792px #ffd700, 779px 1034px #ffd700,
    1333px 1194px #ffd700, 1546px 188px #ffd700, 947px 1906px #ffd700,
    1769px 1595px #ffd700, 1667px 724px #ffd700, 699px 1979px #ffd700,
    194px 213px #ffd700, 1984px 1738px #ffd700, 466px 1754px #ffd700,
    1998px 1908px #ffd700, 975px 1413px #ffd700, 1505px 752px #ffd700,
    1341px 932px #ffd700, 1260px 812px #ffd700, 1591px 50px #ffd700,
    1655px 1730px #ffd700, 1955px 1340px #ffd700, 859px 1742px #ffd700,
    1608px 1931px #ffd700, 1431px 1227px #ffd700, 1553px 222px #ffd700,
    684px 1866px #ffd700, 371px 59px #ffd700, 1885px 869px #ffd700,
    1887px 1795px #ffd700, 1032px 1431px #ffd700, 1085px 1840px #ffd700,
    817px 1343px #ffd700, 1260px 1135px #ffd700, 124px 1566px #ffd700,
    1975px 1980px #ffd700;
}
#title {
  position: absolute;
  top: 5%;
  left: 0;
  right: 0;
  color: #ffd700;
  text-align: center;
  font-family: "lato", sans-serif;
  font-weight: 300;
  font-size: 50px;
  letter-spacing: 10px;
  padding-left: 10px;
}
#title span {
  background: -webkit-linear-gradient(white, #ffd700);
  -webkit-background-clip: text;
  -webkit-text-fill-color: transparent;
}
@keyframes animStar {
  from {
    transform: translateY(0px);
  }
  to {
    transform: translateY(-2000px);
  }
}

.start {
  width: 80px;
  height: 40px;
  position: absolute;
  top: 90%;
  left: 45%;
  background-color: transparent;
  color: aliceblue;
  margin-right: 20px;
}

.clear {
  width: 80px;
  height: 40px;
  position: absolute;
  top: 90%;
  left: 50%;
  background-color: transparent;
  color: aliceblue;
}

.prizeNum {
  width: 80px;
  height: 40px;
  position: absolute;
  top: 90%;
  left: 55%;
  background-color: transparent;
  color: rgba(204, 69, 28, 0.863);
}
#J_import {
  width: 200px;
  height: 40px;
  position: absolute;
  top: 90%;
  left: 30%;
  background-color: transparent;
  color: aliceblue;
}
</style>
