/*TRİE (METİN AĞACI) YAPISI NEDİR?
Metin ağaçları, onu düğümün kendisinden sonra gelen harfi İşaret Ettiği  ağaçlardır . Basitçe ağacın Üzerine Bir metin kodlanabilir ziyaretinde bu Metni veren ağacın uzerinde tek Bir Yol Izlenebilir (deterministik).
Metin ağaçlarının (tray), ikili arama ağaçlarına göre önemli diğer avantajları bir metni aramanın, metin boyutu Kadar Işlem gerektirmesidir. 
İkili arama ağaçlarında bu süre log n Kadar varkit almaktadır. Buradaki n, ağaçtaki düğüm sayısıdır dolayısıyla ikili arama ağaçları , 
ağaçtaki bilgiye göre hızlı veya yavaş çalışırken, metin ağaçları, ağaçta ne kadar bilgi bulunduğundan bağımsız olarak çalışırlar. Metin ağaçları hafızayı da Verimli kullanırlar çünkü 
bir metin ağacının en derin noktası, ağaç üzerindeki en uzun metin kadardır. Ayrıca metin ağaçları en uzun önek eşlemesi gibi problemlerin çözümünde de avantaj Sağlar

TRİE (METİN AĞACININ ÇALIŞMA ŞEKLİ VE ALGORİTMASI)
Trie metin ağacı çalışma şekli şu adımlardan oluşur:
1.	Kök Düğüm: Trie'nin başlangıç noktasıdır ve hiçbir karakteri temsil etmez.
2.	Kelimelerin Eklenmesi: Trie'ye bir kelime eklerken, kelimenin her bir karakteri sırayla trie ağacına eklenir. Her karakter, bir düğüm oluşturulur ve bu düğümün üst düğümü olan önceki karakterin
 alt düğümü olarak eklenir. Bu işlem kelimenin sonuna kadar tekrarlanır ve sonunda kelimenin sonunu belirten bir bayrak ayarlanır.
3.	Kelimelerin Aranması: Kelimenin trie'de olup olmadığını kontrol etmek için trie ağacı taranır. Arama işlemi, her karakter için trie ağacında ilgili alt düğümleri izleyerek gerçekleştirilir.
Eğer aranan kelime trie'de bulunursa ve son düğümün sonu bayrağı ayarlanmışsa, kelimenin trie'de olduğu sonucu döndürülür.*/

/*ALGORİTMASI*/

#include <stdio.h>
#include <stdlib.h>
#include <stdbool.h>
	
 #define ALFABE_BOYUTU 29 
	
struct TrieDugumu {
    char karakter;
    bool kelimeSonu;
    struct TrieDugumu* cocuk;
    struct TrieDugumu* sagKardes;
};
	
	struct TrieDugumu* dugumOlustur(char karakter) {
	    struct TrieDugumu* dugum = (struct TrieDugumu*)malloc(sizeof(struct TrieDugumu));
	    dugum->karakter = karakter;
     dugum->kelimeSonu = false;
     dugum->cocuk = NULL;
    dugum->sagKardes = NULL;
   return dugum;
   }
	
	void ekle(struct TrieDugumu* kok, const char* kelime) {
	    struct TrieDugumu* gezgin = kok;
     int i = 0;
	    while (kelime[i] != '\0') {
	        char harf = kelime[i];
	        struct TrieDugumu* altDugum = gezgin->cocuk;
	        struct TrieDugumu* onceki = NULL;
	        while (altDugum != NULL && altDugum->karakter != harf) {
	            onceki = altDugum;
	            altDugum = altDugum->sagKardes;
	        }
	        if (altDugum == NULL) {
	            struct TrieDugumu* yeniDugum = dugumOlustur(harf);
	            if (onceki == NULL)
	                gezgin->cocuk = yeniDugum;
	            else
	                onceki->sagKardes = yeniDugum;
	            gezgin = yeniDugum;
	        }
	        else {
	            gezgin = altDugum;
	        }
	        i++;
	    }
	    gezgin->kelimeSonu = true;
	}
	
	bool ara(struct TrieDugumu* kok, const char* kelime) {
	    struct TrieDugumu* gezgin = kok;
	    int i = 0;
	    while (kelime[i] != '\0') {
	        char harf = kelime[i];
	        struct TrieDugumu* altDugum = gezgin->cocuk;
	        while (altDugum != NULL && altDugum->karakter != harf) {
	            altDugum = altDugum->sagKardes;
	        }
	        if (altDugum == NULL)
	            return false;
	        gezgin = altDugum;
	        i++;
	    }
	    return (gezgin != NULL && gezgin->kelimeSonu);
	}
	
	int main() {
	    struct TrieDugumu* kok = dugumOlustur('\0');
	
	    ekle(kok, "merhaba");
	    ekle(kok, "dünya");
	    ekle(kok, "elma");
	    ekle(kok, "armut");
	
	    printf("%s\n", ara(kok, "merhaba") ? "Evet" : "Hayir");   
	    printf("%s\n", ara(kok, "dünya") ? "Evet" : "Hayir");    
	    printf("%s\n", ara(kok, "elma) ? "Evet" : "Hayir");   
	    printf("%s\n", ara(kok, "armut") ? "Evet" : "Hayir");   
	
	    return 0;
	}

/*C DİLİYLE YAZILMIŞ ÖRNEK BİR TRİE OLUŞTURMA KODU*/

#include <stdio.h>
#include <stdlib.h>
#include <stdbool.h>
#include <string.h>

#define ALFABE_BOYUTU 30 

struct TrieDugumu {
    struct TrieDugumu* cocuklar[ALFABE_BOYUTU];
    bool kelimeSonu;
};

struct TrieDugumu* dugumOlustur() {
    struct TrieDugumu* dugum = (struct TrieDugumu*)malloc(sizeof(struct TrieDugumu));
    dugum->kelimeSonu = false;
    for (int i = 0; i < ALFABE_BOYUTU; i++)
        dugum->cocuklar[i] = NULL;
    return dugum;
}

void ekle(struct TrieDugumu* kok, const char* kelime) {
    struct TrieDugumu* gezgin = kok;
    for (int i = 0; kelime[i] != '\0'; i++) {
        int index;
        if (kelime[i] >= 'a' && kelime[i] <= 'z')
            index = kelime[i] - 'a';
        else if (kelime[i] == 'ç')
            index = 26;
        else if (kelime[i] == 'ğ')
            index = 27;
        else if (kelime[i] == 'ı' || kelime[i] == 'i')
            index = 28;
        else if (kelime[i] == 'ö')
            index = 29;
        else if (kelime[i] == 'ş')
            index = 30;
        else if (kelime[i] == 'ü')
            index = 31;
        else
            return;
        if (!gezgin->cocuklar[index])
            gezgin->cocuklar[index] = dugumOlustur();
        gezgin = gezgin->cocuklar[index];
    }
    gezgin->kelimeSonu = true;
}

bool ara(struct TrieDugumu* kok, const char* kelime) {
    struct TrieDugumu* gezgin = kok;
    for (int i = 0; kelime[i] != '\0'; i++) {
        int index;
        if (kelime[i] >= 'a' && kelime[i] <= 'z')
            index = kelime[i] - 'a';
        else if (kelime[i] == 'ç')
            index = 26;
        else if (kelime[i] == 'ğ')
            index = 27;
        else if (kelime[i] == 'ı' || kelime[i] == 'i')
            index = 28;
        else if (kelime[i] == 'ö')
            index = 29;
        else if (kelime[i] == 'ş')
            index = 30;
        else if (kelime[i] == 'ü')
            index = 31;
        else
            return false; 
        if (!gezgin->cocuklar[index])
            return false;
        gezgin = gezgin->cocuklar[index];
    }
    return (gezgin != NULL && gezgin->kelimeSonu);
}

int main() {
    struct TrieDugumu* kok = dugumOlustur();

    ekle(kok, "merhaba");
    ekle(kok, "dunya");
    ekle(kok, "kalem");
    ekle(kok, "kitap");

    printf("%s\n", ara(kok, "merhaba") ? "Evet" : "Hayir");  
    printf("%s\n", ara(kok, "dunya") ? "Evet" : "Hayir");     
    printf("%s\n", ara(kok, "elmalar") ? "Evet" : "Hayir");   
    printf("%s\n", ara(kok, "kitap") ? "Evet" : "Hayir");   

    return 0;
}
