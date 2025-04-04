<template>
  <div class="container">
      <div class="nav">
          <a href="#home">Home</a>
          <a href="#about">About</a>
          <a href="#education">Education</a>
      </div>
      <div class="content">
          <section id="home" class="section">
            <h1>My Profile</h1>
            <div class="content-home">
                <img src="../src/assets/S__63340549.jpg" alt="Profile Picture" class="profile-img">
                <div class="info-box">
                    <h2 class="center-align">information</h2>
                    <p class="center-align">ชื่อ:นวพล ปานประเสริฐแสง</p>
                    <p class="center-align">รหัสนิสิต: 6630250214</p>
                    <p class="center-align">จบจากโรงเรียน : ศรีราชา</p>
                    <p class="left-align">ปัจจุบันศึกษาอยู่ที่มหาวิทยาลัยเกษตรศาสตร์ ศรีราชา</p>
                </div>
            </div>
          </section>
    
          <section id="about" class="section">
            <h1>About</h1>
            <div class="content-about">
              <div class="info-box">
                <ul class="list-group list-group-flush text-start bg-dark text-white center-list">
                <li class="list-group-item bg-dark text-white"><strong>ชื่อ :</strong> {{ profile.Name }}</li>
                <li class="list-group-item bg-dark text-white"><strong>รหัสนิสิต :</strong> {{ profile.studentid }}</li>
                <li class="list-group-item bg-dark text-white"><strong>รหัสสาขา :</strong> {{ profile.majorID }}</li>
                <li class="list-group-item bg-dark text-white"><strong>สาขา :</strong> {{ profile.majorName }}</li>
                <li class="list-group-item bg-dark text-white"><strong>โรงเรียนเก่า :</strong> {{ profile.oldschool }}</li>
                </ul>
                <button class="btnedit" @click="editStudent">แก้ไข</button>
              </div>
              <div v-if="editProfileMode" class="popup-overlay">
                <div class="popup">
                    <h3>แก้ไขประวัติส่วนตัว</h3>
                    <label>ชื่อ:</label>
                    <input type="text" v-model="profile.Name" />
                    
                    <label>รหัสนิสิต:</label>
                    <input type="text" v-model="profile.studentid" />
                    
                    <label>รหัสสาขา:</label>
                    <input type="text" v-model="profile.majorID" />
                    
                    <label>สาขา:</label>
                    <input type="text" v-model="profile.majorName" />
                    
                    <label>โรงเรียนเก่า:</label>
                    <input type="text" v-model="profile.oldschool" />
                    
                    <div class="popup-buttons">
                        <button @click="saveProfile">บันทึก</button>
                        <button @click="editProfileMode = false">ยกเลิก</button>
                    </div>
                </div>
            </div>
              <img src="../src/assets/S__63340550.jpg" alt="Profile Picture" class="profile-img">        
            </div>   
          </section>
    
          <section id="education" class="section">
            <h1>รายวิชา</h1>
                <div class="content-education">
                    <div class="table-container">
                        <table>
                            <thead>
                            <tr> 
                                <th>รหัสวิชา</th>
                                <th>ชื่อวิชา</th>
                                <th>หน่วยกิต</th>
                                <th>เกรด</th>
                                <th>จัดการ</th>
                            </tr>
                            </thead>
                            <tbody>
                            <tr v-for="subject in subjects" :key="subject.id">
                                <td>{{ subject.id }}</td>
                                <td>{{ subject.name }}</td>
                                <td>{{ subject.credits }}</td>
                                <td>{{ subject.grade }}</td>
                                <td>
                                <button @click="editSubject(subject)">แก้ไข</button>
                                <button @click="removeSubject(subject.id)">ลบ</button>
                                </td>
                            </tr>
                            </tbody>
                        </table>
                        </div>
                        <button @click="openAddPopup">เพิ่ม</button>
                </div>
                <div v-if="editSubjectMode" class="popup1-overlay">
                <div class="popup1">
                <h3>{{ isAdding ? "เพิ่มรายวิชา" : "แก้ไขรายวิชา" }}</h3>
        
                <label>รหัสวิชา:</label>
                <input type="text" v-model="editedSubject.id" />
        
                <label>ชื่อวิชา:</label>
                <input type="text" v-model="editedSubject.name" />
        
                <label>หน่วยกิต:</label>
                <input type="number" v-model="editedSubject.credits" />
        
                <label>เกรด:</label>
                <input type="text" v-model="editedSubject.grade" />
        
                <div class="popup1-buttons">
                    <button @click="saveSubject">บันทึก</button>
                    <button @click="editSubjectMode = false">ยกเลิก</button>
                </div>
                </div>
            </div>
          </section>
        </div>
    </div>
</template>

<script>
export default {
    data() {
      return {
        profile: {
          studentid: "6630250214",
          Name: "Nawaphon Panprasertsang",
          majorID: "SO6",
          majorName: "Computer Science",
          oldschool: "Sriracha School",
        },
        id_present: "",
        subjects: [],  // ย้ายออกจาก profile
        editProfileMode: false,  // แยก editMode ของ Profile
        editSubjectMode: false,  // แยก editMode ของ Subjects
        editedSubject: { id: "", name: "", credits: 0, grade: "" },
        isAdding: false
      };
    },
    methods: {
      async saveProfile() {
        try {
          const response = await fetch("http://localhost:3000/user", {
            method: "PUT",
            headers: {
              "Content-Type": "application/json",
            },
            body: JSON.stringify(this.profile),
          });
  
          if (response.ok) {
            this.editProfileMode = false;  // ปิด modal หลังบันทึก
          } else {
            alert("บันทึกข้อมูลไม่สำเร็จ");
          }
        } catch (error) {
          console.error("Error saving profile:", error);
        }
      },
      editStudent() {
        this.editProfileMode = true;  // เปิด modal ของ Profile
      },
      async fetchSubjects() {
        try {
          const response = await fetch("http://localhost:3000/subjects");
          this.subjects = await response.json();
        } catch (error) {
          console.error("Error fetching subjects:", error);
        }
      },
      openAddPopup() {
        this.editedSubject = { id: "", name: "", credits: 0, grade: "" };
        this.isAdding = true;
        this.editSubjectMode = true;
      },
      async removeSubject(id) {
        try {
          await fetch(`http://localhost:3000/subjects/${id}`, { method: "DELETE" });
          this.fetchSubjects();  // โหลดข้อมูลใหม่
        } catch (error) {
          console.error("Error deleting subject:", error);
        }
      },
      editSubject(subject) {
        this.editedSubject = { ...subject };
        this.id_present = subject.id;
        this.isAdding = false;
        this.editSubjectMode = true;
      },
      async saveSubject() {
        try {
          if (this.isAdding) {
            await fetch("http://localhost:3000/subjects", {
              method: "POST",
              headers: { "Content-Type": "application/json" },
              body: JSON.stringify(this.editedSubject)
            });
          } else {
            await fetch(`http://localhost:3000/subjects/${this.id_present}`, {
              method: "DELETE",
              headers: { "Content-Type": "application/json" },
            });

            await fetch("http://localhost:3000/subjects", {
              method: "POST",
              headers: { "Content-Type": "application/json" },
              body: JSON.stringify(this.editedSubject)
            });
          }
          this.editSubjectMode = false;
          this.fetchSubjects(); 
        } catch (error) {
          console.error("Error saving subject:", error);
        }
      }
    },
    async mounted() {
      try {
        const response = await fetch("http://localhost:3000/user");
        const data = await response.json();
        if (data) this.profile = data;
        this.fetchSubjects();  // โหลด subjects ทันทีที่หน้าโหลด
      } catch (error) {
        console.error("Error fetching profile:", error);
      }
    }
}
</script>

<style>
    .popup-overlay {
        position: fixed;
        top: 0;
        left: 0;
        width: 100%;
        height: 100%;
        background: rgba(0, 0, 0, 0.5);
        display: flex;
        justify-content: center;
        align-items: center;
    }

    .popup {
        background: white;
        padding: 20px;
        text-align: left;
        width: 800px;
        color: black;
    }

    .popup label {
        display: block;
        margin-top: 10px;
        margin-bottom: 10px;
        text-align: center;
    }

    .popup input {
        width: 100%;
        padding: 5px;
        margin-bottom: 10px;
        border: 1px solid #ccc;
        border-radius: 5px;
    }

    .popup-buttons {
        display: flex;
        justify-content: space-between;
    }
    .info-box li{
        margin-bottom: 30px;
    }

    button {
        margin-top: 10px;
        padding: 8px;
        background-color: greenyellow;
        color: white;
        border: none;
        cursor: pointer;
        border-radius: 5px;
    }

    button:hover {
        background-color: green;
    }
    .btnedit {
      width: 150px;
      margin-left: 50px;
    }
    .table-container {
    max-height: 400px;
    overflow-y: auto;
    margin-top: 20px;
  }
  
  table {
    width: 100%;
    border-collapse: collapse;
  }
  
  th, td {
    border: 1px solid white;
    padding: 8px;
    text-align: center;
  }
  
  button {
    margin: 5px;
    padding: 5px 10px;
    border: none;
    cursor: pointer;
    border-radius: 5px;
  }
  
  button:first-child {
    background-color: orange;
    color: white;
  }
  
  button:last-child {
    background-color: red;
    color: white;
  }
  
  .popup1-overlay {
    position: fixed;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    background: rgba(0, 0, 0, 0.5);
    display: flex;
    justify-content: center;
    align-items: center;
  }
  
  .popup1 {
    background: white;
    padding: 20px;
    text-align: left;
    width: 800px;
    color: black;
  }
  
  .popup1 label {
    display: block;
    margin-top: 10px;
    margin-bottom: 10px;
    text-align: center;
  }
  
  .popup1 input {
    width: 100%;
    padding: 5px;
    margin-bottom: 10px;
    border: 1px solid #ccc;
    border-radius: 5px;
  }
  
  .popup1-buttons {
    display: flex;
    justify-content: space-between;
  }
  
  .popup1-buttons button {
    padding: 8px;
  }
  body {
      background-color: black;
      font-family: "Link", sans-serif;
      margin: 0;
      padding: 0;
      color: azure;
      overflow: hidden;
  }
  html {
      scroll-behavior: smooth;
  }
  .nav {
      background-color: #333333;
      position: fixed;
      height: 100%;
      width: 15%;
  }
  .nav a {
      display: block;
      color: rgb(255, 255, 255);
      text-decoration: none;
      padding: 20px;
      font-size: 1.2rem;
      text-align: center;
      transition: 0.3s;
      text-shadow: 0 0 10px rgba(255, 0, 76, 1), 0 0 20px rgba(255, 0, 76, 0.8), 0 0 30px rgba(255, 0, 76, 0.6); /* เพิ่มแสงรอบตัวหนังสือ */
      
  }
  .nav a:hover {
      background-color: #1b1b1b;
  }
  .content {
      width: 85%;
      margin-left: 15%; 
      padding: 20px;
  }
  .section {
      margin-bottom: 100px;
      padding: 20px;
      margin-left: 20px;
      margin-right: 20px;
      height: 100vh;
  }
  .section h1 {
      text-align: center;
      padding-top: 30px;
      font-size: 2rem;
      text-shadow: 0 0 10px rgba(255, 0, 76, 1), 0 0 20px rgba(255, 0, 76, 0.8), 0 0 30px rgba(255, 0, 76, 0.6); /* เพิ่มแสงรอบตัวหนังสือ */
  }
  .content-home {
      text-align: center;
      display: flex;
      padding-top: 30px;
      padding-left: 200px;
      gap: 50px;
  }
  .content-home img {
      width: 300px;
      height: 300px;
      object-fit: cover;
      border-radius: 360px;
      border: 2px solid #ff0055;
      box-shadow: 0 0 10px 2px rgba(255, 0, 76, 1);
  }
  .info-box {
      padding: 10px;
      border: 2px solid #ff0055;
      border-radius: 10px;
      box-shadow: 0 0 10px 2px rgba(255, 0, 76, 1); /* ลดความโปร่งใสของเงาและขนาดของเงา */
      width: 400px;
      padding-bottom: 30px;
  }
  .content-about img {
      width: 300px;
      height: 300px;
      object-fit: cover;
      border-radius: 360px;
      margin-top: 20px;
      border: 2px solid #ff0055;
      box-shadow: 0 0 10px 2px rgba(255, 0, 76, 1);
  }
  .content-about {
      display: flex;
      padding-top: 30px;
      padding-left: 200px;
      gap: 50px;
  }
  .content-education img {
      width: 150px;
      height: 150px;
      object-fit: cover;
      border-radius: 360px;
      border: 2px solid #ff0055;
      box-shadow: 0 0 10px 2px rgba(255, 0, 76, 1);
  }
  .content-education {
      text-align: center;
  }
  h2 {
      text-shadow: 0 0 10px rgba(255, 0, 76, 1), 0 0 20px rgba(255, 0, 76, 0.8), 0 0 30px rgba(255, 0, 76, 0.6); /* เพิ่มแสงรอบตัวหนังสือ */
  }
</style>