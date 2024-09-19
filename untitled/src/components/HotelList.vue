
<template>
<div>
  <header>
    <img alt="anya cute" src="../assets/anya.png" width="125" height="125" />
  </header>
   <h1>Hotel List</h1>
  <el-table
      :data="tableData"
      :cell-style="{ textAlign: 'center' }"
      :header-cell-style="{ 'text-align': 'center' }"
      stripe
      style="width: 80%; margin: 0 auto; table-layout: auto;"
  >
    <el-table-column prop="name" label="Hotel Name"></el-table-column>
    <el-table-column prop="city" label="City"></el-table-column>
    <el-table-column prop="district" label="District"></el-table-column>
    <el-table-column prop="date" label="Date" :formatter="formatDate"></el-table-column>
    <el-table-column prop="time" label="Earliest check-in Time" :formatter="formatTime"></el-table-column>
    <el-table-column prop="price" label="Price"></el-table-column>
    <el-table-column prop="type" label="Room Type"></el-table-column>
    <el-table-column label="Operations">
      <template #default="scope">
        <el-icon
            class="delete-icon"
            @click="handleDelete(scope.$index)"
        >
          <CloseBold />
        </el-icon>
      </template>
    </el-table-column>

  </el-table>
  <el-button class="el-button" style="margin-left: 0%; margin-top: 20px" type="primary" @click="dialogVisible=true">Add Hotel</el-button>

  <el-dialog
      v-model="dialogVisible"
      title="Add A New Hotel Item"
      width="60%"
  >
    <el-form
        :model="hotelForm"
        :rules="rules"
        ref="hotelForm"
        label-width="170px"
    >
      <el-form-item label="Hotel Name" prop="name">
        <el-input v-model="hotelForm.name" placeholder="Please input the hotel name"></el-input>
      </el-form-item>
      <el-form-item label="City" prop="city">
        <el-radio-group v-model="hotelForm.city">
          <el-radio label="GUANG ZHOU">GUANG ZHOU</el-radio>
          <el-radio label="SHEN ZHEN">SHEN ZHEN</el-radio>
        </el-radio-group>
      </el-form-item>
      <el-form-item label="District" prop="district">
        <el-select v-model="hotelForm.district" placeholder="Please select the district">
          <el-option
              v-for="district in filteredDistricts"
              :key="district"
              :label="district"
              :value="district"
          ></el-option>
        </el-select>
      </el-form-item>
      <el-form-item label="Date" >
        <el-date-picker
            :disabledDate="disabledDate"
            v-model="hotelForm.date"
            type="date"
            placeholder="Please pick a date"
            clearable
        ></el-date-picker>
      </el-form-item>
      <el-form-item label="Earliest-check-in-Time" prop="time">
        <el-time-picker
            v-model="hotelForm.time"
            placeholder="Please pick a time"
            format="HH:mm"
            value-format="HH:mm"
            :picker-options="{ step: '00:15' }"
        ></el-time-picker>

      </el-form-item>
      <el-form-item label="Price" prop="price">
        <el-input v-model="hotelForm.price" placeholder="Please input the price"></el-input>
      </el-form-item>
      <el-form-item label="Room Type" prop="type">
        <el-select v-model="hotelForm.type" placeholder="Please input the room type">
          <el-option label="Standard Room" value="Standard Room" />
          <el-option label="King Bed Room" value="King Bed Room" />
          <el-option label="Family Room" value="Family Room" />
          <el-option label="Presidential Suite" value="Presidential Suite" />
        </el-select>
      </el-form-item>

    </el-form>
    <div style="text-align: center; margin-top: 20px;">
      <el-button type="primary" @click="AddHotel('hotelForm')">Add Hotel</el-button>
      <el-button @click="dialogVisible=false">Cancel</el-button>
    </div>
    </el-dialog>

</div>
</template>

<script>
import {CloseBold} from "@element-plus/icons-vue";

export default {
  name: 'HotelList',
  components: {CloseBold},
  data() {
    const nameValidator = (rule, value, callback) => {
      const reg = /^[A-Za-z]+$/;
      if (value === '') {
        callback(new Error('Please input the hotel name'));
      } else if (!reg.test(value)) {
        callback(new Error('The hotel name must consist of English letters only'));
      } else {
        callback();
      }
    };

    const priceValidator = (rule, value, callback) => {
      const reg = /^[1-9]\d*$/;
      if (value === '') {
        callback(new Error('Please input the price'));
      } else if (!reg.test(value)) {
        callback(new Error('Please input a valid number'));
      } else {
        callback();
      }
    };
    return {
      tableData: [
        {
          name: 'Sheraton',
          city: 'SHEN ZHEN',
          district: 'FU TIAN',
          date: '2024-09-20',
          time: '14:00',
          price: '953',
          type: 'Standard Room',
        },
        {
          name: 'Intercontinental',
          city: 'SHEN ZHEN',
          district: 'YAN TIAN',
          date: '2021-09-02',
          time: '13:00',
          price: '3940',
          type: 'King Bed Room',
        },
      ],
      hotelForm: {
        name: '',
        city: '',
        district: '',
        date: '',
        time: '',
        price: '',
        type: '',
      },
      districts: {
        'GUANG ZHOU': ['YUE XIU', 'LI WAN', 'HAI ZHU', 'TIAN HE', 'BAI YUN', 'HUANG PU', 'FAN YU', 'HUA DU', 'NAN SHA', 'CONG HUA', 'ZENG CHENG'],
        'SHEN ZHEN': ['FU TIAN', 'NAN SHAN', 'LUO HU', 'LONG GANG', 'PING SHAN', 'LONG HUA', 'GUANG MING', 'YAN TIAN'],
      },
      rules: {
        name: [
          { validator: nameValidator, trigger: ['blur', 'change'] },
          { required: true, trigger: true }
        ],
        city: [
          { required: true, message: 'Please input the city', trigger: ['blur', 'change'] },
        ],
        district: [
          { required: true, message: 'Please input the district', trigger: ['blur', 'change'] },
        ],
        time: [
          { required: true, message: 'Please input the time', trigger: ['blur', 'change'] },
        ],
        price: [
          { validator: priceValidator, trigger: ['blur', 'change'] },
          { required: true, trigger: true }
        ],
        type: [
          { required: true, message: 'Please input the room type', trigger: ['blur', 'change'] },
        ],
      },
      dialogVisible: false,
    };
  },
  watch: {
    'hotelForm.city'() {
      this.hotelForm.district = '';
    },
    dialogVisible(val){
      if (!val) {
        this.$nextTick(() => {
          if(this.$refs.hotelForm) {
            this.$refs.hotelForm.resetFields();
          }
        });
      }
    }


  },
  computed: {
    filteredDistricts() {
      return this.districts[this.hotelForm.city] || [];
    }
  },
  methods: {
    handleDelete(index) {
      this.tableData.splice(index, 1);
    },
    AddHotel(formName) {
      this.$refs[formName].validate((valid) => {
        if (valid) {
          const duplicateRow = this.tableData.find(item =>
              item.name === this.hotelForm.name &&
              item.city === this.hotelForm.city &&
              item.district === this.hotelForm.district &&
              item.type === this.hotelForm.type
          );

          if (duplicateRow) {
            this.$message.error('The same Hotel Name, City, District and Room Type already exists.');
            return false;
          }

          // 校验是否有相同的 Hotel Name, City, District 但不同 Room Type，且价格相同
          const sameHotelDiffRoomType = this.tableData.find(item =>
              item.name === this.hotelForm.name &&
              item.city === this.hotelForm.city &&
              item.district === this.hotelForm.district &&
              item.type !== this.hotelForm.type &&  // Room Type 必须不同
              item.price === this.hotelForm.price   // 价格不能相同
          );

          if (sameHotelDiffRoomType) {
            this.$message.error('Price must be different for the same Hotel Name, City and District but different Room Types.');
            return false;
          }

          this.tableData.push({ ...this.hotelForm });
          this.dialogVisible = false;
          this.$message.success('Hotel added successfully!');
        } else {
          console.log('error submit!!');
        }
      });
    },
    disabledDate(date) {
      const today = new Date();
      return date < today.setHours(0, 0, 0, 0);
    },
    formatDate(row, column, cellValue) {
      if (!cellValue) return '';
      const date = new Date(cellValue);
      return date.toLocaleDateString();
    },
    formatTime(row, column, cellValue) {
      if (!cellValue) return '';
      const date = new Date(`1970-01-01T${cellValue}`);
      return date.toLocaleTimeString([], { hour: '2-digit', minute: '2-digit' });
    },
  },
};
</script>


<style scoped>
@import url('https://fonts.googleapis.com/css2?family=Lobster&display=swap');

.container {
  text-align: center;
  padding: 40px 20px;
}
header img {
  display: block;
  margin: 0 auto 20px;
  transition: transform 0.3s;
}

header img:hover {
  transform: scale(1.1);
}


h1 {
  font-family: 'Lobster', cursive;
  font-size: 48px;
  background: linear-gradient(90deg, #42b983, #2c3e50);
  -webkit-background-clip: text;
  -webkit-text-fill-color: transparent;
  position: relative;
  display: inline-block;
  margin-bottom: 20px;
  transition: transform 0.3s;
}

h1::after {
  content: '';
  position: absolute;
  left: 50%;
  bottom: -10px;
  transform: translateX(-50%);
  width: 400%;
  height: 4px;
  background-color: #42b983;
  border-radius: 2px;
}

h1:hover {
  transform: scale(1.05);
}

.el-button {
  margin-left: 0%;
  margin-top: 20px;
  transition: background-color 0.3s, transform 0.3s;
}
.el-button:hover {
  background-color: #42b983;
  transform: scale(1.1);
}

.delete-icon {
  cursor: pointer;
  font-size: 20px;
  color: #f56c6c;
  transition: color 0.3s;
}

.delete-icon:hover {
  transform: scale(1.1);
  color: #ff0000;
}

:deep(.el-dialog__title) {
  font-family: 'Lobster', cursive;
  font-size: 24px;
  transition: 0.3s;
}
:deep(.el-dialog__title:hover) {
  color: #42b983;
  font-size: 32px;
}



a {
  color: #42b983;
}
</style>