<!DOCTYPE html>
<html lang="tr">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>5B'NİN SANAL DİNAMOMETRESİ</title>
    <!-- Tailwind CSS CDN (v3) -->
    <script src="https://cdn.tailwindcss.com"></script>
    <style>
        /* Ekstra stil ve Inter fontu */
        @import url('https://fonts.googleapis.com/css2?family=Inter:wght@400;600;700&display=swap');
        body {
            font-family: 'Inter', sans-serif;
        }
        
        #spring-visual {
            position: relative; 
            width: 20px;
            height: 64px; /* DÜZELTME: Artık yükseklik sabit */
            margin-left: auto;
            margin-right: auto;
            background-image: repeating-linear-gradient(
                to bottom,
                #6b7280, /* gray-500 */
                #6b7280 4px,
                #d1d5db 4px, /* gray-300 */
                #d1d5db 8px
            );
            border-bottom: 2px solid #4b5563;
            /* DÜZELTME: Yükseklik animasyonu kaldırıldı */
            transition: none; 
            z-index: 5;
        }

        /* İç tüp "görünmez" bir taşıyıcı */
        #inner-tube {
            position: relative;
            width: 48px; /* w-12 */
            z-index: 6;
            margin-left: auto;
            margin-right: auto;
        }
        
        /* Ölçeğin kendisi "ŞERİT" */
        #scale {
            position: relative; /* DÜZELTME: Artık 'relative' */
            width: 32px; /* w-8 (İnce) */
            margin: 0 auto;
            padding: 4px 0;
            background-color: #D1D5DB; /* bg-gray-300 */
            border-radius: 6px; /* rounded-md */
            border: 1px solid #6B7280; /* border-gray-500 */
            
            /* DÜZELTME: Yükseklik sabitlendi, flex kaldırıldı */
            height: 472px; /* 21 * 22px + 10px padding */

            font-family: 'Inter', sans-serif;
            font-weight: 600; /* font-semibold */
            color: #1F2937; /* text-gray-800 */
        }
        
        /* DÜZELTME: Tüm span'lar artık 'absolute' pozisyonda */
        #scale span {
            position: absolute;
            width: 100%;
            text-align: center;
            font-size: 10px; 
            line-height: 10px; /* Yazı yüksekliği */
            left: 0;
        }

        /* Kanca */
        #hook {
            position: relative;
            width: 32px; /* w-8 */
            height: 32px; /* h-8 */
            border-bottom-width: 4px;
            border-left-width: 4px;
            border-right-width: 4px;
            border-color: #374151; /* border-gray-700 */
            border-bottom-left-radius: 9999px; /* rounded-b-full */
            border-bottom-right-radius: 9999px; /* rounded-b-full */
            background-color: #E5E7EB; /* bg-gray-200 */
            left: 50%;
            transform: translateX(-50%);
            margin-top: -4px; /* Ölçeğe yapışsın */
            z-index: 7;
        }
        
        /* Hareketli grup (ince) */
        #inner-cylinder-group {
            transition: top 0.5s ease-in-out;
            position: absolute;
            width: 56px; /* w-14 */
            z-index: 10; /* Maskenin (z-20) ARKASINDA */
            left: 50%;
            transform: translateX(-50%);
            top: 0px; 
        }

        /* Kütle (ince) */
        #attached-mass {
            transition: top 0.5s ease-in-out;
            position: absolute;
            left: 50%;
            transform: translateX(-50%);
            z-index: 30; /* En önde */
            width: 64px; /* w-16 */
        }

        /* Aktif butonu vurgula */
        .weight-btn.active {
            transform: scale(0.95);
            opacity: 0.7;
            box-shadow: inset 0 2px 4px rgba(0,0,0,0.3);
        }

    </style>
</head>
<body class="bg-gray-100 min-h-screen flex items-center justify-center p-4">

    <!-- max-w-4xl ve grid -->
    <div class="w-full max-w-4xl bg-white rounded-2xl shadow-xl p-6 md:p-8">
        
        <!-- DÜZELTME: Başlık sola dayalı ve <br> eklendi -->
        <h1 class="text-2xl md:text-3xl font-bold text-left text-gray-800 mb-6 md:col-span-3">
            5B'NİN<br>SANAL DİNAMOMETRESİ
        </h1>

        <!-- 3 sütunlu grid yapısı -->
        <div class="grid md:grid-cols-3 gap-6 items-start">
            
            <!-- SÜTUN 1: KONTROLLER -->
            <div class="md:col-span-1 md:pt-16">
                <h2 class="text-lg font-semibold mb-4 text-gray-700">Kütle Seçimi (kg)</h2>
                <!-- Kontrol Butonları (Grid-cols-6) -->
                <div class="grid grid-cols-6 gap-2">
                    <button class="weight-btn bg-gray-400 hover:bg-gray-500 text-white font-semibold py-1 px-2 text-sm rounded-lg shadow-md transition-all duration-200 col-span-2" data-kg="0">
                        Boş
                    </button>
                    <!-- Kütle Butonları -->
                    <button class="weight-btn bg-teal-500 hover:bg-teal-600 text-white font-semibold py-1 px-2 text-sm rounded-lg shadow-md transition-all duration-200" data-kg="1">1</button>
                    <button class="weight-btn bg-teal-500 hover:bg-teal-600 text-white font-semibold py-1 px-2 text-sm rounded-lg shadow-md transition-all duration-200" data-kg="2">2</button>
                    <button class="weight-btn bg-teal-500 hover:bg-teal-600 text-white font-semibold py-1 px-2 text-sm rounded-lg shadow-md transition-all duration-200" data-kg="3">3</button>
                    <button class="weight-btn bg-teal-500 hover:bg-teal-600 text-white font-semibold py-1 px-2 text-sm rounded-lg shadow-md transition-all duration-200" data-kg="4">4</button>
                    <button class="weight-btn bg-green-500 hover:bg-green-600 text-white font-semibold py-1 px-2 text-sm rounded-lg shadow-md transition-all duration-200" data-kg="5">5</button>
                    <button class="weight-btn bg-green-500 hover:bg-green-600 text-white font-semibold py-1 px-2 text-sm rounded-lg shadow-md transition-all duration-200" data-kg="6">6</button>
                    <button class="weight-btn bg-green-500 hover:bg-green-600 text-white font-semibold py-1 px-2 text-sm rounded-lg shadow-md transition-all duration-200" data-kg="7">7</button>

                    <button class="weight-btn bg-green-500 hover:bg-green-600 text-white font-semibold py-1 px-2 text-sm rounded-lg shadow-md transition-all duration-200" data-kg="8">8</button>
                    <button class="weight-btn bg-green-500 hover:bg-green-600 text-white font-semibold py-1 px-2 text-sm rounded-lg shadow-md transition-all duration-200" data-kg="9">9</button>
                    <button class="weight-btn bg-green-500 hover:bg-green-600 text-white font-semibold py-1 px-2 text-sm rounded-lg shadow-md transition-all duration-200" data-kg="10">10</button>
                    <button class="weight-btn bg-yellow-500 hover:bg-yellow-600 text-white font-semibold py-1 px-2 text-sm rounded-lg shadow-md transition-all duration-200" data-kg="11">11</button>
                    <button class="weight-btn bg-yellow-500 hover:bg-yellow-600 text-white font-semibold py-1 px-2 text-sm rounded-lg shadow-md transition-all duration-200" data-kg="12">12</button>
                    <button class="weight-btn bg-yellow-500 hover:bg-yellow-600 text-white font-semibold py-1 px-2 text-sm rounded-lg shadow-md transition-all duration-200" data-kg="13">13</button>
                    <button class="weight-btn bg-yellow-500 hover:bg-yellow-600 text-white font-semibold py-1 px-2 text-sm rounded-lg shadow-md transition-all duration-200" data-kg="14">14</button>
                    <button class="weight-btn bg-yellow-500 hover:bg-yellow-600 text-white font-semibold py-1 px-2 text-sm rounded-lg shadow-md transition-all duration-200" data-kg="15">15</button>
                    <button class="weight-btn bg-orange-500 hover:bg-orange-600 text-white font-semibold py-1 px-2 text-sm rounded-lg shadow-md transition-all duration-200" data-kg="16">16</button>
                    <button class="weight-btn bg-orange-500 hover:bg-orange-600 text-white font-semibold py-1 px-2 text-sm rounded-lg shadow-md transition-all duration-200" data-kg="17">17</button>
                    <button class="weight-btn bg-orange-500 hover:bg-orange-600 text-white font-semibold py-1 px-2 text-sm rounded-lg shadow-md transition-all duration-200" data-kg="18">18</button>
                    <button class="weight-btn bg-orange-500 hover:bg-orange-600 text-white font-semibold py-1 px-2 text-sm rounded-lg shadow-md transition-all duration-200" data-kg="19">19</button>
                    <button class="weight-btn bg-red-500 hover:bg-red-600 text-white font-semibold py-1 px-2 text-sm rounded-lg shadow-md transition-all duration-200 col-span-2" data-kg="20">
                        20 kg
                    </button>
                </div>
            </div>
            
            <!-- SÜTUN 2: SİMÜLASYON (MASKELEME YÖNTEMİ) -->
            <div class="md:col-span-1">
                <!-- Simülasyon Alanı -->
                <div id="simulation-area" class="flex justify-center items-start h-[700px] relative">
                    
                    <!-- 1. SABİT Askı (Ayrı, en önde) -->
                    <div class="absolute left-1/2 -translate-x-1/2 w-10 h-6 bg-gray-600 rounded-b-md flex items-center justify-center z-30" style="top: -6px;">
                         <div class="w-4 h-4 rounded-full border-2 border-white bg-gray-500"></div>
                    </div>

                    <!-- 2. SABİT Dış Gövde (MASKE - İnce) -->
                    <!-- DÜZELTME: 'top' ve 'height' değiştirildi -->
                    <div id="outer-cylinder" class="absolute w-20 bg-teal-600 border-2 border-gray-900 rounded-lg shadow-inner z-20" 
                         style="top: -234px; height: 514px; left: 50%; transform: translateX(-50%);">
                        <!-- h-64 (256px) kaldırıldı, style'a eklendi -->
                        <!-- top: 24px -> -234px (INITIAL_GROUP_TOP_PX ile eşleşti) -->
                        <!-- height: 256px -> 514px (READ_LINE_PX - INITIAL_GROUP_TOP_PX) -->
                    </div>
                        
                    <!-- 3. HAREKETLİ Grup (MASKENİN ARKASINDA - z-index: 10) -->
                    <div id="inner-cylinder-group">
        
                        <!-- Esnek Yay (Görsel) -->
                        <div id="spring-visual"></div>
        
                        <!-- Hareket Eden İç Çubuk (Taşıyıcı) -->
                        <div id="inner-tube">
                            <!-- Şerit Ölçek (0-200, 10'ar artan) -->
                            <div id="scale">
                                <!-- DÜZELTME: Konumlar artık 'style' ile sabitlendi -->
                                <!-- 22px * 10N aralık (10N = 200, 190...) -->
                                <span style="top: 10px;">200N</span> 
                                <span style="top: 32px;">190</span>
                                <span style="top: 54px;">180</span> 
                                <span style="top: 76px;">170</span>
                                <span style="top: 98px;">160N</span> 
                                <span style="top: 120px;">150</span>
                                <span style="top: 142px;">140</span> 
                                <span style="top: 164px;">130</span>
                                <span style="top: 186px;">120N</span> 
                                <span style="top: 208px;">110</span>
                                <span style="top: 230px;">100</span> 
                                <span style="top: 252px;">90</span>
                                <span style="top: 274px;">80N</span>  
                                <span style="top: 296px;">70</span>
                                <span style="top: 318px;">60</span>  
                                <span style="top: 340px;">50</span>
                                <span style="top: 362px;">40N</span>  
                                <span style="top: 384px;">30</span>
                                <span style="top: 406px;">20</span>  
                                <span style="top: 428px;">10</span>
                                <span id="span-0N" style="top: 450px;">0 N</span> <!-- 0N çizgisi -->
                            </div>
                        </div> <!-- inner-tube sonu -->
        
                        <!-- Kanca (İç tüpün altında - HAREKETLİ) -->
                        <div id="hook"></div>
        
                    </div> <!-- inner-cylinder-group sonu -->

                    <!-- 4. Eklenen Kütle Görseli (AYRI HAREKET EDER - z-index: 30) -->
                    <div id="attached-mass" class="bg-gray-700 rounded-md shadow-lg hidden z-30" style="border: 2px solid #374151; top: 376px;">
                        <span id="mass-label" class="text-white text-center block font-bold p-2"></span>
                    </div>
                </div>
            </div>

            <!-- SÜTUN 3: OKUMA -->
            <div class="md:col-span-1 md:pt-16">
                <h2 class="text-lg font-semibold mb-4 text-gray-700">Ölçüm Sonucu</h2>
                <!-- Ölçüm Göstergesi -->
                <div class="text-left">
                    <span id="reading" class="text-lg font-semibold block">
                        <!-- İçerik JS ile doldurulacak -->
                    </span>
                </div>
            </div>

        </div> <!-- Grid Wrapper sonu -->
    </div>

    <script>
        window.onload = function() {
            
            // Gerekli HTML elementlerini seç
            const outerCylinder = document.getElementById('outer-cylinder');
            const innerGroup = document.getElementById('inner-cylinder-group');
            const spring = document.getElementById('spring-visual');
            const hook = document.getElementById('hook');
            const innerTube = document.getElementById('inner-tube');
            const scale = document.getElementById('scale');
            
            const reading = document.getElementById('reading');
            const buttons = document.querySelectorAll('.weight-btn');
            const attachedMass = document.getElementById('attached-mass');
            const massLabel = document.getElementById('mass-label');

            const GRAVITY = 10.0;
            
            // Kalibrasyon değişkenlerini 'let' olarak tanımla
            let READ_LINE_PX, OFFSET_0_N_PX, pixelsPerNewton;
            let INITIAL_GROUP_TOP_PX, INITIAL_SPRING_HEIGHT_PX;
            let HOOK_OFFSET_IN_GROUP, MASS_OFFSET_FROM_HOOK;
            
            // Kalibrasyonun yapılıp yapılmadığını takip et
            let isCalibrated = false;

            /**
             * KALİBRASYON FONKSİYONU
             * Sadece ilk tıklamada çalışır.
             */
            function calibrate() {
                // 1. Okuma çizgisi (Dış kılıfın alt kenarı)
                // DÜZELTME: Değerler elle girildi (Hardcoded)
                // 280px (Dış kılıfın -234px'ten başlayıp 514px uzadığı yer)
                READ_LINE_PX = -234.0 + 514.0; // = 280px. 
                                              // Önceki kod (24 + 256 = 280) ile AYNI.

                // 2. İç Silindir (Ölçek)
                // DÜZELTME: Artık 'offsetTop' okuması yok, sabit değerler kullanılıyor
                
                // Her 10N, 22px'lik bir mesafedir (CSS'e göre: 450px - 428px = 22px)
                const PIXELS_PER_10N = 22.0;
                pixelsPerNewton = PIXELS_PER_10N / 10.0; // 2.2 px/N
                
                // "0 N" çizgisinin 'top' değeri (CSS'e göre sabit)
                const pos_0N_relative = 450.0; 
                
                // "0 N" çizgisinin, hareketli grubun (inner-group) tepesinden uzaklığı:
                // (Yay + iç tüp + ölçek + 0N'nin konumu)
                // DÜZELTME: Değerler elle girildi
                const springHeight = 64.0; // #spring-visual height
                const innerTubeOffset = 0.0; // #inner-tube
                const scaleOffset = 0.0; // #scale
                
                OFFSET_0_N_PX = springHeight + innerTubeOffset + scaleOffset + pos_0N_relative;
                // OFFSET_0_N_PX = 64 + 0 + 0 + 450 = 514px
                
                // 3. Başlangıç Konumu (0N'da)
                INITIAL_GROUP_TOP_PX = READ_LINE_PX - OFFSET_0_N_PX;
                // INITIAL_GROUP_TOP_PX = 280 - 514 = -234px

                // 4. Sabit Değerler
                INITIAL_SPRING_HEIGHT_PX = spring.offsetHeight; 
                HOOK_OFFSET_IN_GROUP = hook.offsetTop;
                MASS_OFFSET_FROM_HOOK = hook.offsetHeight - 4; // 4px border
                
                isCalibrated = true; // Kalibrasyon tamamlandı
            }

            let currentActiveButton = document.querySelector('.weight-btn[data-kg="0"]');
            if (currentActiveButton) {
                currentActiveButton.classList.add('active');
            }

            // Dinamometreyi güncelleyen TEK fonksiyon
            function updateDynamometer(kg) {
                // *** İlk tıklamada kalibre et ***
                if (!isCalibrated) {
                    calibrate();
                    // Kalibrasyondan sonra başlangıç pozisyonunu ayarla
                    innerGroup.style.top = `${INITIAL_GROUP_TOP_PX}px`;
                }

                const mass = parseFloat(kg);
                const newtons = mass * GRAVITY;

                // 1. Ne kadar 'sarkacağını' (yer değiştirme) hesapla
                const downwardDisplacement = newtons * pixelsPerNewton;
                
                // 2. İç grubun yeni 'top' pozisyonunu hesapla
                const newGroupTop = INITIAL_GROUP_TOP_PX + downwardDisplacement;
                
                // 3. İç grubun 'top' stilini güncelle (AŞAĞI SARKMA)
                innerGroup.style.top = `${newGroupTop}px`;
                
                // 4. *** 2 KAT HATA DÜZELTMESİ ***
                // Bu satır hataya neden oluyordu, kaldırıldı.
                // spring.style.height = `${INITIAL_SPRING_HEIGHT_PX + downwardDisplacement}px`;

                // 5. Metin göstergesini güncelle
                reading.innerHTML = `
                    <span class="text-gray-600 block mb-2">Kütle: <span class="font-bold text-lg">${mass} kg</span></span> 
                    <span class="text-gray-600 block">Kuvvet:</span> 
                    <span class="text-blue-600 font-bold text-2xl">${newtons.toFixed(1)} N</span>`;
                
                // 6. Görsel kütleyi (AYRI) güncelle
                if (mass === 0) {
                    attachedMass.classList.add('hidden');
                } else {
                    attachedMass.classList.remove('hidden');
                    attachedMass.style.height = `${30 + mass * 2}px`;
                    massLabel.textContent = `${mass} kg`;
                    
                    // Kütlenin yeni mutlak 'top' pozisyonu
                    // DÜZELTME: hook.offsetTop 'isCalibrated' içinde hesaplanmalı.
                    // (Ancak ilk tıklamada calibrate() çalıştığı için bu sorun olmamalı)
                    const newMassTop = newGroupTop + (isCalibrated ? HOOK_OFFSET_IN_GROUP : 536) + MASS_OFFSET_FROM_HOOK;
                    attachedMass.style.top = `${newMassTop}px`;
                }
            }
            
            // Tüm butonlara tıklama olayı ekle
            buttons.forEach(button => {
                button.addEventListener('click', () => {
                    if (currentActiveButton) {
                        currentActiveButton.classList.remove('active');
                    }
                    button.classList.add('active');
                    currentActiveButton = button;

                    const kg = button.dataset.kg;
                    // updateDynamometer'ı çalıştır (gerekirse kalibre edecek)
                    updateDynamometer(kg);
                });
            });

            // Başlangıçta göstergeyi ayarla (kalibrasyon YAPMADAN)
            reading.innerHTML = `
                <span class="text-gray-600 block mb-2">Kütle: <span class="font-bold text-lg">0</span> kg</span> 
                <span class="text-gray-600 block">Kuvvet:</span> 
                <span class="text-blue-600 font-bold text-2xl">0.0 N</span>`;
            
            // Başlangıçta "Boş" butonuna tıkla (ve kalibre et)
            document.querySelector('.weight-btn[data-kg="0"]').click();
        
        }; // window.onload sonu
    </script>

</body>
</html>

