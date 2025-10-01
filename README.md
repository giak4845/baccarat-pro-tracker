from PIL import Image, ImageDraw, ImageFont

def tao_anh_thong_ke(nguoi, nha, hoa, tong, goi_y):
    # Tạo ảnh nền đen
    img = Image.new("RGB", (600, 500), (20, 20, 20))
    draw = ImageDraw.Draw(img)

    # Font (cần có Arial hoặc font Unicode trên máy bạn)
    font_title = ImageFont.truetype("arial.ttf", 36)
    font_text = ImageFont.truetype("arial.ttf", 28)
    font_big = ImageFont.truetype("arial.ttf", 40)

    # Vẽ tiêu đề
    draw.text((150, 20), "📊 THỐNG KÊ VIP PROMAX", font=font_title, fill=(255, 215, 0))

    # Nội dung
    y = 100
    draw.text((50, y), f"👤 CÁI (Player):   {nguoi} ván | {round(nguoi/tong*100,1)}%", font=font_text, fill=(0, 200, 255))
    y += 60
    draw.text((50, y), f"🏠 CON (Banker):  {nha} ván | {round(nha/tong*100,1)}%", font=font_text, fill=(255, 70, 70))
    y += 60
    draw.text((50, y), f"🤝 HÒA:           {hoa} ván | {round(hoa/tong*100,1)}%", font=font_text, fill=(255, 215, 0))

    # Tổng số ván
    y += 80
    draw.text((50, y), f"🔢 TỔNG VÁN: {tong}", font=font_text, fill=(255, 255, 255))

    # Gợi ý ván sau nổi bật
    y += 80
    draw.text((50, y), f"🎯 DỰ ĐOÁN VÁN SAU: {goi_y}", font=font_big, fill=(50, 255, 100))

    # Lưu ảnh
    img.save("thong_ke_vip.png")
    print("✅ Đã tạo ảnh: thong_ke_vip.png")

# Test thử với dữ liệu mẫu
tao_anh_thong_ke(6, 12, 1, 19, "🏠 CON")
