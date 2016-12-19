# tmoza
load "$NCARG_ROOT/lib/ncarg/nclscripts/csm/shea_util.ncl"

begin

a    = addfile("toc_melbjan.nc","r") ; read in january melbourne ozone data from ACCESS-CCM

toc  = a->field34172

dimt = dimsizes(toc)                
toc_2=rm_single_dims(toc)

toc_1Dtrend = toc_2
toc_1Dtrend = dtrend_msg(ispan(1,1560,1),toc_2,False,False) ; removes trend
printVarSummary(toc_2)

toc_3 = reshape (toc_1Dtrend, (/52,30/)) ; reshape array into 52 years each with 30 days          
iarr=new((/52,5/),float) ;  fill with minimum, 25th percentile, median, 75th percentile, maximum of each timeseries

do i=0, 51
qsort(toc_3(i,:))
dimt = dimsizes(toc_3(i,:))
x25  = round(.25*dimt,3)-1     ; -1 to account for NCL indexing starting
x75  = round(.75*dimt,3)-1     ; at 0              
iarr(i,:) = (/min(toc_3(i,:)),toc_3(i,x25(0)),dim_median(toc_3(i,:)),toc_3(i,x75(0)),max(toc_3(i,:))/)  
end do

printVarSummary(iarr)
printMinMax(iarr,True)  

qsort(toc_2) 
toc_4 = toc_2(0:77) ; lists lowest 5% ofozone values
print(toc_2(0:77))
print(toc_4)
print(toc_2&time_1(0:77))

wks_type = "png"
wks_type@wkWidth = 2000
wks_type@wkHeight = 2000
wks = gsn_open_wks(wks_type,"box_1")

res            = True                         ; plot mods desired
res@tiMainString = "Total Column Ozone for January - Melbourne"
res@tiYAxisString = "Column Ozone DU"
res@tiXAxisString = "Year"printMinMax(iarr,True)

res@gsnMaximize = True  
res@trXMinF = min(1960)-1  
res@trXMaxF = max(2011)+1  
res@tmXBMode = "Manual"  
res@tmXBTickStartF = res@trXMinF

time = ispan(1960,2011,1)

mres = True                                   ; ploymarkers for low ozone days
mres@gsMarkerColor = "navy blue"              ; change color
mres@gsMarkerIndex = 1                        ; change style
mres@gsMarkerSizeF = 40.                      ; change size\
; add low ozone event details for polymarkers (year and value)

plot = boxplot(wks,time,iarr,False,res,False)  

dum1 = gsn_add_polymarker(wks,plot,1975,286.9176,mres) 
dum2 = gsn_add_polymarker(wks,plot,1978,282.6299,mres)
dum3 = gsn_add_polymarker(wks,plot,1990,284.6059,mres) 
dum4 = gsn_add_polymarker(wks,plot,1992,288.6948,mres) 
dum5 = gsn_add_polymarker(wks,plot,1995,274.6491,mres)
dum6 = gsn_add_polymarker(wks,plot,1996,280.1182,mres) 
dum7 = gsn_add_polymarker(wks,plot,1997,287.3031,mres) 
dum8 = gsn_add_polymarker(wks,plot,1999,286.644,mres)
dum9 = gsn_add_polymarker(wks,plot,2000,282.9974,mres) 
dum10 = gsn_add_polymarker(wks,plot,2003,277.9825,mres) 
dum11 = gsn_add_polymarker(wks,plot,2008,283.8294,mres)
dum12 = gsn_add_polymarker(wks,plot,2009,279.4088,mres)
dum13 = gsn_add_polymarker(wks,plot,2010,281.6338,mres) 
dum14 = gsn_add_polymarker(wks,plot,2011,288.0508,mres)         

draw(plot)                                    ; boxplot does not call these  
frame(wks) 
end  
