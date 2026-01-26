>>> import xarray as xr
>>> import matplotlib.pyplot as plt
>>> import cartopy.crs as ccrs
>>> import cartopy.feature as cfeature
>>> import numpy as np
>>> ds_anlik=xr.open_dataset(r"C:\Users\Public\harita\data_instant.nc")
>>> data=ds_anlik.sel(valid_time="2026-01-08T12:00:00")
>>> ws=np.sqrt(data.u10*2 + data.v10*2)
>>> basinc_hpa=data.msl/100
>>> fig=plt.figure(figsize=(12,8), dpi=100)
>>> ax=plt.axes(projection=ccrs.PlateCarree())
>>> ax.set_extent([25.0,32.5,39.0,43.0])
>>> ax.add_feature(cfeature.COASTLINE, linewidth=1.2, color='black')
>>> ax.add_feature(cfeature.BORDERS, linestyle=':', color='gray')
>>> cp=ax.pcolormesh(data.longitude, data.latitude, ws, cmap='YlOrRd', alpha=0.8)
>>> cb=plt.colorbar(cp, shrink=0.6)
>>> cb.set_label("Rüzgar Hızı (M/S)")
>>> contours = ax.contour(data.longitude, data.latitude, basinc_hpa, colors='black', linewidths=0.7)
>>> plt.clabel(contours, inline=True, fontsize=8)
>>> plt.title('İstanbul 8 Ocak 2026 Fırtına Analizi - Deniz Çabuk')
>>> plt.show()
