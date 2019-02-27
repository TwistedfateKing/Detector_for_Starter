
ssd

# SSD
 * \>>>>[amdegroot/ssd.pytorch](https://github.com/amdegroot/ssd.pytorch)<<<< 이거 제일 좋음
 
 주의사항
 1. COCO, VOC2007, VOC2012 DATA 생성 필요 링크에서 잘 설명하고 있음.
 2. weights/vgg16_reducedfc.pth [다운로드](https://drive.google.com/open?id=1TVfe1LIqlOz0KDdJ2dptovWsv1z3lqqr)


pytorch  낮은 버전에서 작성 된 것으로 보임.
일단 Run해 보고 아래와 같은 에러 발생시 적용 바람.

1. layers/modules/multibox_loss.py파일에서 dimension Error

        # Hard Negative Mining
        loss_c[pos] = 0  # filter out pos boxes for now
        loss_c = loss_c.view(num, -1)   ... 를

        # Hard Negative Mining
        loss_c = loss_c.view(num, -1)
        loss_c[pos] = 0  # filter out pos boxes for now
        loss_c = loss_c.view(num, -1)   ... 로 변

2. train.py <문법 오류> data[0]은 사라짐

        loc_loss += loss_l.data[0] 를
        loc_loss += loss_l.item() 으로 변경

3. train.py <data loader 문제>

        batch_iterator = iter(data_loader)
        for iteration in range(args.start_iter, cfg['max_iter']):
        if args.visdom and iteration != 0 and (iteration % epoch_size == 0):
            update_vis_plot(epoch, loc_loss, conf_loss, epoch_plot, None,
                            'append', epoch_size)
            # reset epoch loss counters
            loc_loss = 0
            conf_loss = 0
            epoch += 1

	부분을 아래로 변경 

	    batch_iterator = None
	    for iteration in range(args.start_iter, cfg['max_iter']):
	        if (not batch_iterator) or (iteration % epoch_size ==0):
	            batch_iterator = iter(data_loader)
	            loc_loss = 0
	            conf_loss = 0
	            epoch += 1
