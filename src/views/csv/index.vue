<template>
  <div style="border: 1px solid red; width: 100%; height: 200px">
    <el-button type="primary" @click="exportAllToCSV">json 转 csv</el-button>
    <el-button type="primary" @click="exportAllToCSV('photoList')">json.photoList 转 csv</el-button>
    <el-button type="warning" @click="jsonToXlSX('newJson')">newJson 转 xlsx</el-button>
    <el-button type="success" @click="jsonToXlSX">json 转 xlsx</el-button>
    <el-button type="success" @click="jsonToXlSX('photoList')">json.photoList 转 xlsx</el-button>
    <el-button type="success" @click="jsonToXlSX('picList')">json.picList 转 xlsx</el-button>
  </div>
</template>

<script>
import * as XLSX from "xlsx";
export default {
  name: "csv",
  data() {
    return {};
  },
  props: {},
  components: {},
  created() {},
  mounted() {},
  methods: {
    jsonToXlSX(type) {
      const files = require.context("@/3paiJSON", false, /\.json$/);
      const jsonData = [];
      files.keys().forEach((key) => {
        files(key).forEach((y) => {
          if (type == "newJson") {
            jsonData.push(y);
          } else if (type == "picList") {
            jsonData.push(y);
          } else {
            // let { geom, htkRecordList, points, ...newItem } = y;
            // newItem.gxsj = newItem.gxsj.toString();
            // newItem.pcsj = newItem.pcsj.toString();
            // newItem.crjs = newItem.crjs.toString();
            // jsonData.push(newItem);
            jsonData.push(y);
          }
        });
      });
      console.log(jsonData.length);
      let convertedData = [];
      let xlsxName = "";
      if (type == "photoList") {
        convertedData = jsonData
          .map((item) => item.photoList)
          .flat()
          .map((y) => ({ fileName: y.fileName, extension: y.extension, filePath: `"${y.filePath}",`, groupId: y.groupId, fileId: y.fileId }));
        xlsxName = "photoList";
      } else if (type == "picList") {
        convertedData = jsonData.map((item) => item.picList).flat();
        xlsxName = "picList";
      } else if (type == "newJson") {
        convertedData = jsonData;
        xlsxName = "newJson";
      } else {
        convertedData = jsonData.map((item) => {
          const { picList, ...newItem } = item;
          return newItem;
        });
        xlsxName = "json";
      }
      const worksheet = XLSX.utils.json_to_sheet(convertedData);
      const workbook = XLSX.utils.book_new();
      XLSX.utils.book_append_sheet(workbook, worksheet, "Sheet1");
      XLSX.writeFile(workbook, `${xlsxName}.xlsx`);
    },

    exportAllToCSV(type) {
      const files = require.context("@/json", false, /\.json$/);
      const jsonData = [];
      files.keys().forEach((key) => {
        files(key).forEach((y) => {
          if (type == "newJson") {
            jsonData.push(y);
          } else {
            let { geom, htkRecordList, points, ...newItem } = y;
            jsonData.push(newItem);
          }
        });
      });
      let csvData = "";
      let flattenedArray = [];
      let csvName = "";
      if (type == "photoList") {
        flattenedArray = jsonData
          .map((x) => x.photoList)
          .flat()
          .map((item) => ({
            fileName: item.fileName,
            extension: item.extension,
            filePath: `"${item.filePath}",`,
            groupId: item.groupId,
            fileId: item.fileId,
          }));
        csvData = this.JSONToCSV(flattenedArray);
        csvName = "photoList";
      } else if (type == "newJson") {
        flattenedArray = jsonData;
        csvData = this.JSONToCSV(flattenedArray);
        csvName = "json";
      } else {
        flattenedArray = jsonData.map((item) => {
          const { photoList, ...newItem } = item;
          return newItem;
        });
        csvData = this.JSONToCSV(flattenedArray);
        csvName = "json";
      }
      this.downloadCSV(csvData, `${csvName}.csv`);
    },
    downloadCSV(csvData, filename) {
      const blob = new Blob([csvData], { type: "text/csv" });
      const url = URL.createObjectURL(blob);

      const link = document.createElement("a");
      link.href = url;
      link.setAttribute("download", filename);
      document.body.appendChild(link);
      link.click();
      document.body.removeChild(link);
      URL.revokeObjectURL(url);
    },
    JSONToCSV(jsonData) {
      const flattenObject = (obj, prefix = "") => {
        return Object.keys(obj).reduce((acc, key) => {
          const pre = prefix.length ? prefix + "." : "";
          if (typeof obj[key] === "object" && obj[key] !== null) {
            Object.assign(acc, flattenObject(obj[key], pre + key));
          } else {
            acc[pre + key] = obj[key];
          }
          return acc;
        }, {});
      };
      const flattenData = jsonData.map((obj) => flattenObject(obj));
      const headers = Array.from(new Set(flattenData.flatMap((obj) => Object.keys(obj))));
      const csvData = [headers.join(","), ...flattenData.map((obj) => headers.map((field) => obj[field] || "").join(","))];
      return csvData.join("\n");
    },
  },
};
</script>

<style lang="scss" scoped></style>
