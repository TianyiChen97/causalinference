library('reshape2')

withmis=read.csv('/Users/chentianyi/Desktop/Research /Winter Research/sythea data 2 /2021_01_29T17_07_29Z_1/patients.csv')
withoutmis=read.csv('/Users/chentianyi/Desktop/Research /Winter Research/sythea data 2 /2021_01_29T20_03_14Z_0/patients.csv')
withmis$mismatch=1
withoutmis$mismatch=0
pp=rbind(withmis,withoutmis)
length(unique(pp$Id))
sum((pp$mismatch==1))
sum((pp$mismatch==0))


pp$BIRTHDATE=as.Date(pp$BIRTHDATE)
pp$DEATHDATE=as.Date(pp$DEATHDATE)
pp$life_span=pp$DEATHDATE-pp$BIRTHDATE



# analysis of observation data
#####
####################
obpp=rbind(read.csv('/Users/chentianyi/Desktop/Research /Winter Research/sythea data 2 /2021_01_29T17_07_29Z_1/observations.csv')
           ,read.csv('/Users/chentianyi/Desktop/Research /Winter Research/sythea data 2 /2021_01_29T20_03_14Z_0/observations.csv'))

obpp=obpp[,c(2,1,5)]
obpp$DATE=as.Date(obpp$DATE)
sort(table(obpp$DESCRIPTION),decreasing = TRUE)
sort(table(obpp$PATIENT),decreasing = TRUE)
expatient=obpp[which(obpp$PATIENT=='cd1bc633-9749-b6bf-a850-042196eb1103'),]
hemo=obpp[which(obpp$DESCRIPTION=='Hemoglobin A1c/Hemoglobin.total in Blood'|obpp$DESCRIPTION=='Hemoglobin A1c/Hemoglobin.total in Blood-nightshift'),]
length(unique(obpp[which(obpp$DESCRIPTION=='QALY'),2]))
pp[pp$Id %in% unique(hemo$PATIENT),'mismatch']

#####

library(stringr)
processd=function(data){
  colnames(data)=c('PATIENT','START','DESCRIPTION')
  datacast=list()
  datacast[[1]]=dcast(data,PATIENT~DESCRIPTION,value.var = 'START',min)
  datacast[[2]]=dcast(data,PATIENT~DESCRIPTION,value.var = 'START',max)
  datacast[[3]]=dcast(data,PATIENT~DESCRIPTION,value.var = 'START',mean)
  datacast[[4]]=dcast(data,PATIENT~DESCRIPTION,value.var = 'START',median)
  datacast[[5]]=dcast(data,PATIENT~DESCRIPTION,value.var = 'START',length)
  nn=ncol(datacast[[1]])
  type=c('min','max','mean','median','length')
  i=1
  colnames(datacast[[i]])=c('PATIENT',paste(colnames(datacast[[i]])[2:nn],rep(type[i],(nn-1))))
  datafinal=datacast[[1]]
  for (i in 2:5) {
    colnames(datacast[[i]])=c('PATIENT',paste(colnames(datacast[[i]])[2:nn],rep(type[i],(nn-1))))
    datafinal=cbind(datafinal,datacast[[i]][2:nn])
  }
  return(datafinal)
}
######medication counts:

med=rbind(read.csv('/Users/chentianyi/Desktop/Research /Winter Research/sythea data 2 /2021_01_29T17_07_29Z_1/medications.csv'),read.csv('/Users/chentianyi/Desktop/Research /Winter Research/sythea data 2 /2021_01_29T20_03_14Z_0/medications.csv'))
length(unique(med$PATIENT))
med=med[,c(3,1,7)]
med$START=as.Date(med$START)

medfinal=processd(med)

####office-visit(encounters) counts
econ=rbind(read.csv('/Users/chentianyi/Desktop/Research /Winter Research/sythea data 2 /2021_01_29T17_07_29Z_1/encounters.csv'),
                     read.csv('/Users/chentianyi/Desktop/Research /Winter Research/sythea data 2 /2021_01_29T20_03_14Z_0/encounters.csv'))
length(unique(econ$PATIENT))
econ=econ[,c(4,2,8)]
econ$START=as.Date(econ$START)


econfinal=processd(econ)

### observation data 
obpp=rbind(read.csv('/Users/chentianyi/Desktop/Research /Winter Research/sythea data 2 /2021_01_29T17_07_29Z_1/observations.csv')
             ,read.csv('/Users/chentianyi/Desktop/Research /Winter Research/sythea data 2 /2021_01_29T20_03_14Z_0/observations.csv'))
obpp=obpp[which(obpp$TYPE=='numeric'),]
obpp=obpp[,c(2,5,6)]
datacast=list()
data=obpp
datacast[[1]]=dcast(data,PATIENT~DESCRIPTION,value.var = 'VALUE',min)
datacast[[2]]=dcast(data,PATIENT~DESCRIPTION,value.var = 'VALUE',max)
nn=ncol(datacast[[1]])
type=c('min','max')
i=1
colnames(datacast[[i]])=c('PATIENT',paste(colnames(datacast[[i]])[2:nn],rep(type[i],(nn-1))))
datafinal=datacast[[1]]
i=2
colnames(datacast[[i]])=c('PATIENT',paste(colnames(datacast[[i]])[2:nn],rep(type[i],(nn-1))))
datafinal=cbind(datafinal,datacast[[i]][2:nn])

obfinal=datafinal


### diagnosis counts (conditions.csv)
conditions=rbind(read.csv('/Users/chentianyi/Desktop/Research /Winter Research/sythea data 2 /2021_01_29T17_07_29Z_1/conditions.csv'),
      read.csv('/Users/chentianyi/Desktop/Research /Winter Research/sythea data 2 /2021_01_29T20_03_14Z_0/conditions.csv'))
length(unique(conditions$PATIENT))
conditions=conditions[,c(3,1,6)]
conditions$START=as.Date(conditions$START)
confinal=processd(conditions)


### merge together 
ppbirth=pp[,c(1,2,13,14,15,26,27)]
colnames(ppbirth)[1]='PATIENT'
medjoin=merge(medfinal, ppbirth, by = 'PATIENT',type='full')
meeconjoin=merge(medjoin, econfinal, by = 'PATIENT',type='full')
final=merge(confinal, meeconjoin, by = 'PATIENT',type='full')


a=final[,which(grepl('min',colnames(final))|
      grepl('max',colnames(final))|
      grepl('mean',colnames(final))|
      grepl('median',colnames(final)))]

birthday=as.data.frame(matrix(rep(final$BIRTHDATE,ncol(a)),1293,ncol(a)))

final[,which(grepl('min',colnames(final))|
               grepl('max',colnames(final))|
               grepl('mean',colnames(final))|
               grepl('median',colnames(final)))]=a-birthday
final[1,]

library('dummies')

final=cbind(final,dummy('RACE',final),dummy('ETHNICITY',final),dummy('GENDER',final))
final=final[,-c(273:275)]
final=merge(final, obfinal, by = 'PATIENT',type='full')

write.csv(final,'/Users/chentianyi/Desktop/Research /Winter Research/sythea data 2 /non-time series data_withob.csv')

###### some more refine for data

final[which(final == Inf, arr.ind=TRUE)]=0
final[which(final == -Inf, arr.ind=TRUE)]=0
final[which(is.na(final), arr.ind=TRUE)]=0

finala=final[,-1]


for (i in 1:ncol(finala)) {
  finala[,i]=as.numeric(finala[,i])
}

###remove three constant variable
finala=finala[,-which(colnames(finala) %in% c('DALY min','QALY min','QOLS max') )]

##remove perfect linearity 
corrf=as.data.frame(cor(finala))
colname=as.data.frame(sort(table(which(abs(corrf)>.99,arr.ind = T)[,2]),decreasing = T))

finala=finala[,-as.numeric(as.character(colname[which(colname[,2]>1),1]))]
finala=cbind(finala,final$life_span)

corrf=as.data.frame(cor(finala))

write.csv(finala,'/Users/chentianyi/Desktop/Research /Winter Research/sythea data 2 /non-time series data_notcorrelated.csv')

