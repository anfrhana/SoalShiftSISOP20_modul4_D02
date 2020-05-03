Enkripsi versi 1:

Jika sebuah direktori dibuat dengan awalan “encv1_”, maka direktori tersebut akan menjadi direktori terenkripsi menggunakan metode enkripsi v1.

Jika sebuah direktori di-rename dengan awalan “encv1_”, maka direktori tersebut akan menjadi direktori terenkripsi menggunakan metode enkripsi v1.

Apabila sebuah direktori terenkripsi di-rename menjadi tidak terenkripsi, maka isi adirektori tersebut akan terdekrip.
Setiap pembuatan direktori terenkripsi baru (mkdir ataupun rename) akan tercatat ke sebuah database/log berupa file.
Semua file yang berada dalam direktori ter enkripsi menggunakan caesar cipher dengan key.


9(ku@AW1[Lmvgax6q`5Y2Ry?+sF!^HKQiBXCUSe&0M.b%rI'7d)o4~VfZ*{#:}ETt$3J-zpc]lnh8,GwP_ND|jO

Misal kan ada file bernama “kelincilucu.jpg” dalam directory FOTO_PENTING, dan key yang dipakai adalah 10 “encv1_rahasia/FOTO_PENTING/kelincilucu.jpg” => “encv1_rahasia/ULlL@u]AlZA(/g7D.|_.Da_a.jpg

Note : Dalam penamaan file ‘/’ diabaikan, dan ekstensi tidak perlu di encrypt.

Metode enkripsi pada suatu direktori juga berlaku kedalam direktori lainnya yang ada didalamnya.

dibuat fungsi untuk enkripsi dan dekripsi
```
void encryptV1(char *src) 
{
  int len = strlen(src);
  int start = 0;

  for (int i = strlen(src); i >= 0; i--) 
  {
    if(src[i] == '/')
      break;

    if(src[i] == '.')
    {
      len = i - 1;
      break;
    }
  }

  for (int i = 1; i < len; i++)
    if (src[i] == '/')
      start = i;

  for (int i = start; i <= len; i++) 
  {
    if(src[i] == '/')
      continue;

    int caesar_index = 0;
    while(1)
    {
      if(src[i] == caesar[caesar_index])
      {
        src[i] = caesar[caesar_index + 10];
        break;
      }
      caesar_index++;
    }
  }
}

void decryptV1(char *src) 
{
  int len = strlen(src); 
  int start = 0;
    
  for (int i = 1; i < len; i++)
  {
    if(src[i] == '/' || src[i + 1] == '\0') 
    {
      start = i + 1;
      break;
    }
  }

  for (int i = strlen(src); i >= 0; i--)
  {
    if (src[i] == '.') 
    {
      len = i - 1;
      break;
    }
  }

  for (int i = start; i <= len; i++) 
  {
    if(src[i] == '/')
      continue;

    int caesar_index = strlen(caesar) - 1;
    while(1)
    {
      if(src[i] == caesar[caesar_index])
      {
        src[i] = caesar[caesar_index - 10];
        break;
      }
      caesar_index--;
    }
  }
}

```
***

Log system:

Sebuah berkas nantinya akan terbentuk bernama "fs.log" di direktori *home* pengguna (/home/[user]/fs.log) yang berguna menyimpan daftar perintah system call yang telah dijalankan.

Agar nantinya pencatatan lebih rapi dan terstruktur, log akan dibagi menjadi beberapa level yaitu INFO dan WARNING.
Untuk log level WARNING, merupakan pencatatan log untuk syscall rmdir dan unlink.
Sisanya, akan dicatat dengan level INFO.

Format untuk logging yaitu:

[LEVEL]::[yy][mm][dd]-[HH]:[MM]:[SS]::[CMD]::[DESC ...]


LEVEL    : Level logging

yy       : Tahun dua digit

mm       : Bulan dua digit

dd       : Hari dua digit

HH       : Jam dua digit

MM       : Menit dua digit

SS       : Detik dua digit

CMD      : System call yang terpanggil

DESC     : Deskripsi tambahan (bisa lebih dari satu, dipisahkan dengan ::

dibuat fungsi untuk melakukan penulisan log sesuai dengan format
```
void writeLog(char *level, char *cmd_desc)
{
  FILE * fp;
  fp = fopen (log_path, "a+");

  time_t rawtime = time(NULL);
  
  struct tm *info = localtime(&rawtime);
  
  char time[100];
  strftime(time, 100, "%y%m%d-%H:%M:%S", info);

  char log[100];
  sprintf(log, "%s::%s::%s\n", level, time, cmd_desc);
  fputs(log, fp);

  fclose(fp);
}
```
