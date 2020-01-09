---
layout: post
title:  "상태바 알림"
subtitle: "Android 상태바 알림 편집"
date:   2020-01-07 15:43:00 +0900
categories: android
comments: true
background: '/img/posts/06.jpg'
---

```java
int lastShownNotificationId = -1;

/**
 * @param channelId ex) "msshin.notification.CHANNEL_ID_STATE"
 */
private void showNotification(Service service, String channelId, int iconResId, int colorResId, String briefDescription, String titleName, int progress, String contentMsg, int notificationId) {
    final NotificationCompat.Builder builder = getNotificationBuilder(service,
            channelId,
            NotificationManagerCompat.IMPORTANCE_LOW);

    Intent notiIntent = new Intent(App.getInstance(), IntroActivity.class);
    notiIntent.setAction(Intent.ACTION_MAIN);
    notiIntent.addCategory(Intent.CATEGORY_LAUNCHER);
    notiIntent.addFlags(Intent.FLAG_ACTIVITY_NEW_TASK);
    PendingIntent pendingIntent = PendingIntent.getActivity(App.getInstance(), 0, notiIntent, PendingIntent.FLAG_UPDATE_CURRENT);

    builder.setOngoing(true)
            .setSmallIcon(iconResId)
            .setColor(service.getResources().getColor(colorResId))
            .setContentIntent(pendingIntent) // 알람 클릭 시 발생 인텐트
            .setTicker(briefDescription) // 알람 간단한 설명
            .setContentTitle(titleName) // 알람 제목
            .setContentText(contentMsg); // 알람 내용

    if (progress >= 0) {
        int maxProgress = 100;
        builder.setProgress(maxProgress, progress, false);
    }

    Notification notification = builder.build();

    service.startForeground(notificationId, notification);

    if (notificationId != lastShownNotificationId) {
        final NotificationManager nm = (NotificationManager) service.getSystemService(Activity.NOTIFICATION_SERVICE);
        nm.cancel(lastShownNotificationId);
    }
    lastShownNotificationId = notificationId;
}

public static NotificationCompat.Builder getNotificationBuilder(Context context, String channelId, int importance) {
    NotificationCompat.Builder builder;
    if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.O) {
        prepareChannel(context, channelId, importance);
        builder = new NotificationCompat.Builder(context, channelId);
    } else {
        builder = new NotificationCompat.Builder(context);
    }
    return builder;
}
```