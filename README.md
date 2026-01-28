import xarray as xr
import matplotlib.pyplot as plt
import cartopy.crs as ccrs
import cartopy.feature as cfeature
import numpy as np

# 1. Veriyi Açma
ds_anlik = xr.open_dataset(r"C:\Users\Public\harita\data_instant.nc")
data = ds_anlik.sel(valid_time="2026-01-08T12:00:00")

# 2. Hesaplamalar (Birim: km/saat)
# m/s rüzgar hızı: sqrt(u^2 + v^2)
ws_ms = np.sqrt(data.u10**2 + data.v10**2) 
# km/saat'e çevrim: 3.6 ile çarpıyoruz
ws_kmh = ws_ms * 3.6 

basinc_hpa = data.msl / 100

# 3. Görselleştirme
fig = plt.figure(figsize=(12,8), dpi=100) # Tuvali (duvarı) hazırla
ax = plt.axes(projection=ccrs.PlateCarree()) #Duvara astığın tablo çerçevesiplate carrede verinin koordinatlarını enlem boylam olarak kabul et gridde dönüştür
ax.set_extent([25.0, 32.5, 39.0, 43.0]) # şimdi bu çerçevemizin özelliklerini kaydedeceğizax

# Harita Altlığı
ax.add_feature(cfeature.COASTLINE, linewidth=1.2, color='black')
ax.add_feature(cfeature.BORDERS, linestyle=':', color='gray')
ax.add_feature(cfeature.LAND, facecolor='#f5f5f5') # Karaları belirginleştirelim

# 4. Renk ve Ölçek Düzenlemesi (Vmin ve Vmax)
# km/saat cinsinden 0 ile 80 arası skala fırtınayı çok net gösterir
wp = ax.pcolormesh(data.longitude, data.latitude, ws_kmh, 
                   cmap='YlOrRd', vmin=0, vmax=80, alpha=0.8) #pcolormash duvar ustası olarak düşün her koordinatta rüzgar hızı değişeceği için tanımlamamız gerekiyor borders gibi hazır bir özellik değil 

# 5. Legend (Colorbar) Düzenlemesi
cb = plt.colorbar(wp, shrink=0.6, pad=0.03)
cb.set_label('Rüzgar Hızı (km/saat)', fontsize=11, fontweight='bold')

# 6. İzobarlar ve Etiketler
contours = ax.contour(data.longitude, data.latitude, basinc_hpa, 
                      colors='black', linewidths=0.7) #contour basınç izibobar çizgileri içn
plt.clabel(contours, inline=True, fontsize=8, fmt='%1.0f')

plt.title('8 Ocak 2026 - Saat 12:00 Lodos Fırtınası Analizi', fontsize=14)
plt.show()
