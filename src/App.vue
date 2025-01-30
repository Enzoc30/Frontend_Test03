<template>
  <div id="app">
    <h1>Procesar Archivos</h1>

    <!-- Sección para subir archivos ZIP -->
    <div class="file-upload-container">
      <h2>Subir Archivo ZIP</h2>
      <input type="file" @change="handleZipUpload" accept=".zip" class="file-input" />
      <button @click="submitZip" :disabled="isZipLoading" class="upload-button">
        <span v-if="!isZipLoading && !isZipCompleted">Subir Archivo ZIP</span>
        <span v-if="isZipLoading">Cargando...</span>
        <span v-if="isZipCompleted">¡Completado!</span>
      </button>
    </div>

    <!-- Sección para subir archivos Excel -->
    <div class="file-upload-container">
      <h2>Procesar Archivo Excel</h2>
      <input type="file" @change="handleExcelUpload" accept=".xlsx, .xls" class="file-input" />
      <button @click="submitExcel" :disabled="isExcelLoading" class="upload-button">
        <span v-if="!isExcelLoading && !isExcelCompleted">Procesar Archivo Excel</span>
        <span v-if="isExcelLoading">Procesando...</span>
        <span v-if="isExcelCompleted">¡Completado!</span>
      </button>
    </div>

    <!-- Mensajes de error -->
    <p v-if="error" style="color: red;">{{ error }}</p>
  </div>
</template>

<script>
export default {
  data() {
    return {
      // Estados para el archivo ZIP
      zipFile: null,
      isZipLoading: false,
      isZipCompleted: false,

      // Estados para el archivo Excel
      excelFile: null,
      isExcelLoading: false,
      isExcelCompleted: false,

      // Mensaje de error
      error: null,
    };
  },
  methods: {
    // Maneja la selección del archivo ZIP
    handleZipUpload(event) {
      this.zipFile = event.target.files[0];
      this.isZipCompleted = false; // Reinicia el estado de completado
    },

    // Sube el archivo ZIP al backend
    async submitZip() {
      if (!this.zipFile) {
        this.error = "Por favor, selecciona un archivo ZIP.";
        return;
      }

      this.isZipLoading = true;
      this.error = null;

      const formData = new FormData();
      formData.append("file", this.zipFile);

      try {
        const response = await fetch("http://localhost:5000/upload", {
          method: "POST",
          body: formData,
        });

        if (!response.ok) {
          throw new Error("Error al subir el archivo ZIP.");
        }

        const blob = await response.blob();
        const url = window.URL.createObjectURL(blob);
        const link = document.createElement("a");
        link.href = url;
        link.setAttribute("download", this.zipFile.name.replace(/\.[^/.]+$/, "_Modificado.xlsx"));
        document.body.appendChild(link);
        link.click();
        document.body.removeChild(link);
        window.URL.revokeObjectURL(url);

        this.isZipCompleted = true; // Activa el estado de completado
      } catch (error) {
        console.error("Error:", error);
        this.error = "Hubo un error al procesar el archivo ZIP.";
      } finally {
        this.isZipLoading = false; // Desactiva el estado de carga
      }
    },

    // Maneja la selección del archivo Excel
    handleExcelUpload(event) {
      this.excelFile = event.target.files[0];
      this.isExcelCompleted = false; // Reinicia el estado de completado
    },

    // Sube el archivo Excel al backend
    async submitExcel() {
      if (!this.excelFile) {
        this.error = "Por favor, selecciona un archivo Excel.";
        return;
      }

      this.isExcelLoading = true;
      this.error = null;

      const formData = new FormData();
      formData.append("file", this.excelFile);

      try {
        const response = await fetch("http://127.0.0.1:5000/procesar_excel", {
          method: "POST",
          body: formData,
        });

        if (!response.ok) {
          throw new Error("Error al procesar el archivo Excel.");
        }

        const blob = await response.blob();
        const url = window.URL.createObjectURL(blob);
        const link = document.createElement("a");
        link.href = url;
        link.setAttribute("download", this.excelFile.name.replace(/\.[^/.]+$/, "_Modificado.xlsx"));
        document.body.appendChild(link);
        link.click();
        document.body.removeChild(link);
        window.URL.revokeObjectURL(url);

        this.isExcelCompleted = true; // Activa el estado de completado
      } catch (error) {
        console.error("Error:", error);
        this.error = "Hubo un error al procesar el archivo Excel.";
      } finally {
        this.isExcelLoading = false; // Desactiva el estado de carga
      }
    },
  },
};
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  text-align: center;
  margin-top: 60px;
}

.file-upload-container {
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 10px;
  margin-top: 20px;
  padding: 20px;
  border: 1px solid #ccc;
  border-radius: 10px;
  background-color: #f9f9f9;
}

.file-input {
  padding: 10px;
  border: 2px solid #ccc;
  border-radius: 5px;
  background-color: white;
  cursor: pointer;
}

.upload-button {
  padding: 10px 20px;
  border: none;
  border-radius: 5px;
  background-color: #007bff;
  color: white;
  font-size: 16px;
  cursor: pointer;
  transition: background-color 0.3s ease;
}

.upload-button:disabled {
  background-color: #ccc;
  cursor: not-allowed;
}

.upload-button:hover:not(:disabled) {
  background-color: #0056b3;
}

h1 {
  color: #333;
}

h2 {
  color: #555;
  margin-bottom: 10px;
}
</style>