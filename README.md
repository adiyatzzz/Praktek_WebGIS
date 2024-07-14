# Pengenalan

Website <i>Geographic Information System(GIS</i>) untuk perkuliahan Sistem Informasi Geografis.

# Sebelum Memulai

## Aplikasi yang diperlukan

- PostgreSQL versi 14.x | [link download](https://www.postgresql.org/download)
- GeoServer versi 2.24.2 | [link download](https://geoserver.org/release/2.24.2) -> pilih <i>Windows Installer</i>
- QGIS Desktop versi 3.20.2 | [link download](https://qgis.org/en/site/forusers/download.html)

### Install PostgreSQL
- Ikuti instruksi yang telah diberikan untuk menginstall PostgreSQL.
- Ketika selesai menginstall PostgreSQL, jalankan aplikasi <b>Application Stack Builder</b>.
- Ikuti instruksi yang ada dan cari bagian <b>Spatial Extensions</b>, pilih PostGIS 3.x dan klik next untuk meinstall.

### Menyiapkan Database
- Jalankan <b>PgAdmin</b> dan masukkan password yang telah dibuat sebelumnya.
- Buat database baru dengan nama <b>DB_GIS</b> dan berikan deskripsi(optional).
- Pada database <b>DB_GIS</b>, pilih pada bagian <b>Extensions</b> dan pada name pilih <b>postgis</b>, klik save.
- Sekarang pada bagian <b>Schemas</b>, buat schema baru dengan name <b>GIS_4F</b>, klik save.

### Konfigurasi Quantum GIS
- Jalankan <b>QGIS Desktop</b> yang sudah diinstall
- Pada bagian <b>Browser</b> pilih <b>PostGIS</b>, klik kanan <b>-New Connection</b>.
- Isikan <b>Name</b> dengan <i>Perkuliahan_GIS</i>, <b>Host</b> dengan <i>localhost</i>, <b>Port</b> dengan <i>5432</i>, <b>Database</b> dengan <i>DB_GIS</i>.
- Pada bagian <b>Authentication</b>, klik tab <b>Basic</b>, isikan <b>Username</b> dan <b>Password</b> sesuai dengan database yang telah dibuat sebelumnya.
- Klik <b>Test Connection</b> untuk memeriksa koneksi.
- Setelah itu klik <b>OK</b>.

#### Mendapatkan Shapefile
- Shapefile ada pada folder <b>assets/shapefile</b> dengan ekstension <b>.shp</b>.

#### Menampilkan Peta
- Jalankan aplikasi <b>Quantum GIS(QGIS)</b>
- Drag file <b>.shp</b> ke Layers QGIS.

#### Upload Peta ke Postgres
- Buka koneksi <b>Perkuliahan_GIS</b>, pilih Schema dan tarik dari Layers ke Schema tujuan (GIS_4F).
- Tunggu proses upload selesai.

### Menginstall GeoServer
- Diperlukan Java versi 11.x untuk menginstall GeoServer.

### Konfigurasi GeoServer
- Jalankan <b>GeoServer</b> yang sudah diinstall.
- Akses melalui browser http://localhost:8080/geoserver.
- Login (default) dengan username <i>admin</i> dan password <i>geoserver</i>.

#### Konfigurasi GeoServer - Workspace
- Pilih <b>Workspaces</b>.
- klik <b>Add new workspace</b>.
- Buat dengan nama <i>DataGIS</i> dan url `http://localhost:8080/geoserver/DataGIS/wms`.
- Klik <b>Save</b>.

#### Konfigurasi GeoServer - Stores
- Pilih <b>Stores</b>.
- Klik <b>Add new store</b>.
- Pilih <b>PostGIS</b>.
- Isikan <b>Workspace</b> dengan <i>DataGIS</i>, <b>Datasource Name</b> dengan <i>Pg_GIS4F</i>, <b>Host</b> dengan <i>localhost</i>, <b>Port</b> dengan <i>5432</i>, <b>Database</b> dengan <i>DB_GIS</i>, <b>Schema</b> dengan <i>GIS_4F</i>.
- Masukkan <b>User</b> dan <b>Password</b> dari database yang telah dibuat sebelumnya.
- Setelah itu klik <b>Save</b>.

#### Membuat Map Service
- Pilih menu <b>Layers</b>.
- Klik <b>Add new layer</b>.
- Pilih Datasource (DataGIS).
- Klik Tombol <b>Publish</b> pada layer.
- Cari <b>Compute from native bounds</b> pada tab <b>Data</b>, terus diklik.
- Pindah ke tab <b>Publishing</b>, arahkan ke <b>WMS Setting</b> dan perhatikan <b>Default Style</b> tersebut.
- Misal pada Toponimi berupa <b>point</b>.
- Setelah itu, klik <b>Save</b>.

#### Melihat Map Service
- Pilih menu <b>Layer Preview</b>.
- Cari layer yang telah dibuat.
- Klik <b>OpenLayers</b>.
- Jika terlihat, maka sudah selesai.

### Geoserver pada file php
- Untuk menambahkan <b>Map Service</b> baru pada php, silahkan buka file <b>index.php</b>.
- Cari pada bagian:
```php
map = new ol.Map({
...
````
- tambahkan kode berikut pada <i>layers</i>:
```php
new ol.layer.Tile({
  source: new ol.source.TileWMS({
      url: <URL_GEOSERVER>,
      params: { 'LAYERS': 'NAME_PADA_LAYER_PREVIEW' }
    }),
  name : "NAMA_LAYER"
}),
```
- contoh:
```php
new ol.layer.Tile({
  source: new ol.source.TileWMS({
      url: 'http://localhost:8080/geoserver/DataGIS/wms?',
      params: { 'LAYERS': 'DataGIS:Toponimi_PT_50K' }
    }),
  name : "Toponimi"
}),
```