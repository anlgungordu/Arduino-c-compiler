# Arduino-c-compiler

C# ile arduino koduna girmeden ilgili yerlerin değiştirilip arduino derlemek için gerekli kod yapısı.

    try
    {
        //C# kodunun içinde bin\Debug\ içine attığımız arduino kodu .ino uzantılı.
        string startupPath = System.IO.Directory.GetCurrentDirectory();
        DosyaYolu = Environment.CurrentDirectory + "\\ee\\ee.ino";
        //Proje dosyamızdan ilgili ino dosyasını çekiyoruz.
        if (textBox1.Text != null && DosyaYolu!=null) //Dosya yolumuz veya değiştireceğimiz text boş değil ise 
        {
            StringBuilder sb = new StringBuilder();
            string deger = "";
            string[] dosya = File.ReadAllLines(DosyaYolu); // ilgili arduino kodunu satır satır dizi olarak okuyoruz.
            foreach (string satır in dosya) //dizi boyutunca dönecek olan for kodumuz
            {
                if (satır.Contains("delay_sec=")) //değiştirecek olduğumuz arduino kodunun öncesi
                {
                    // arduino kısmında ' int delay_sec=100; ' olarak tanımlı kodumuz var
                    //Biz burada ms kısmını değiştirmek istiyoruz.
                    IcerikBaslangicIndex = satır.IndexOf("=") + 1;
                    IcerikBitisIndex = satır.Substring(IcerikBaslangicIndex).IndexOf(";");
                    //= ve ; arasındaki string yapıyı alacağız
                    if (IcerikBitisIndex != 0)
                    {
                        //IcerikBaslangicIndex = den sonra başladığımız index değerimiz
                        //IcerikBitisIndex ilk değerden sonra kaç ileri gideceğimizi gösteren index değerimiz.
                        degeri = satır.Substring(IcerikBaslangicIndex, IcerikBitisIndex);
                        deger = satır.Replace(degeri, textBox1.Text);
                        //Burada texte yazdığımız ms değerini atıyoruz.
                        sb.Append(deger + "\r\n");
                    }
                    continue; //Ve arduino kodunu yazmaya devam ediyoruz
                }
                sb.Append(satır + "\r\n");
            }
            File.WriteAllText(DosyaYolu, sb.ToString());
            //ms kısmı değişmiş durumda.
            //Sırada arduino'ya girmeden derleme ve çalıştırma işi var.
            //derleme işini cmd kodu ile halledeceğiz.
            string myPath = @"C:\Program Files (x86)\Arduino\arduino"; //Arduinonuzun .exe'nin yüklü olduğu dosya yolu
            System.Diagnostics.Process p = new System.Diagnostics.Process(); //cmd de kod yazmak için alanımız
            p.StartInfo.FileName = myPath; //arduino.exe sini aldık.
            p.StartInfo.Arguments = @"--upload " + DosyaYolu; 
            //Buradaki DosyaYolumuz projenin içine yüklediğimiz .ino nun bulunduğu dosya.
            p.StartInfo.WindowStyle = System.Diagnostics.ProcessWindowStyle.Hidden; //cmd açılmasını engelleme
            p.StartInfo.CreateNoWindow = true; //Pencere Açılmasını engeller
            p.StartInfo.RedirectStandardOutput = true;
            p.StartInfo.UseShellExecute = false;
            p.Start();
            //Derleyip arduinomuza kod yüklendi.
        }
        else
        {
            MessageBox.Show("Dosya yolu yok veya değer girmediniz.");
        }
    }
    catch
    {
        MessageBox.Show("HATA OLUŞTU");
    }
}
